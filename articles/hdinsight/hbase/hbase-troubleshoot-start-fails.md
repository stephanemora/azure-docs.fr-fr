---
title: Échec du démarrage d’Apache HBase Master dans Azure HDInsight
description: Échec du démarrage d’Apache HBase Master (HMaster) dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935452"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Échec du démarrage d’Apache HBase Master (HMaster) dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Scénario : Échec de renommage atomique

### <a name="issue"></a>Problème

Fichiers inattendus identifiés au cours du processus de démarrage.

### <a name="cause"></a>Cause :

Pendant le processus de démarrage, HMaster effectue de nombreuses étapes d’initialisation, notamment le déplacement des données du dossier temporaire (. tmp) vers le dossier de données. HMaster analyse également le dossier des journaux WAL (Write Ahead Log) pour déterminer s’il existe des serveurs de région inactifs. Pendant l’ensemble de ces opérations, il exécute une commande `list` de base sur ces dossiers. S’il rencontre un fichier inattendu dans l’un de ces dossiers, il lève une exception et le démarrage échoue.

### <a name="resolution"></a>Résolution :

Si vous vous trouvez dans cette situation, vérifiez la pile des appels afin de déterminer quel dossier pourrait être responsable du problème (par exemple, le dossier des journaux WAL ou le dossier .tmp). Ensuite, utilisez Cloud Explorer ou des commandes HDFS pour localiser le fichier posant problème. Il s’agit généralement d’un fichier `*-renamePending.json` (un fichier journal utilisé pour implémenter l’opération de renommage atomique dans le pilote WASB). En raison des bogues de cette implémentation, ces fichiers peuvent subsister en cas de plantage du processus. Forcez la suppression du fichier via Cloud Explorer. En outre, il peut arriver qu’un fichier temporaire de type $ se trouve à cet emplacement. Ce fichier n’est pas détectable via Cloud Explorer, mais uniquement via la commande `ls` HDFS. Vous pouvez utiliser la commande `hdfs dfs -rm //\$\$\$.\$\$\$` HDFS pour supprimer ce fichier.

Une fois ce fichier supprimé, HMaster devrait démarrer immédiatement.

---

## <a name="scenario-no-server-address-listed"></a>Scénario : Aucune adresse de serveur listée

### <a name="issue"></a>Problème

Le journal HMaster affiche un message d’erreur semblable à « Aucune adresse de serveur n’est listée dans hbase : meta pour la région xxx ».

### <a name="cause"></a>Cause :

HMaster n’a pas pu être initialisé après le redémarrage de HBase.

### <a name="resolution"></a>Résolution :

1. Exécutez les commandes suivantes sur le shell HBase (modifiez les valeurs le cas échéant) :

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Supprimez l’entrée hbase: namespace, car la même erreur pourrait se reproduire pendant l’analyse de la table hbase: namespace.

1. Redémarrez le service HMaster actif à partir de l’interface utilisateur Ambari pour rétablir le fonctionnement de HBase.

1. Exécutez la commande suivante dans le shell HBase pour afficher toutes les tables hors connexion :

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scénario : java.io.IOException: Timedout

### <a name="issue"></a>Problème

HMaster arrive à expiration avec une exception irrécupérable du type `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Cause :

Le délai d’attente est un défaut connu de HMaster. Les tâches générales de démarrage du cluster peuvent prendre beaucoup de temps. HMaster s’arrête si la table namespace n’a pas encore été affectée. Les tâches de démarrage fastidieuses se produisent quand une grande quantité de données n’a pas été vidée et qu’un délai d’attente de cinq minutes est insuffisant.

### <a name="resolution"></a>Résolution :

1. Dans l’interface utilisateur Ambari, accédez à HBase -> Configs et ajoutez le paramètre suivant dans le `hbase-site.xml` personnalisé :

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Redémarrez les services requis (principalement HMaster et éventuellement d’autres services HBase).

---

## <a name="scenario-frequent-regionserver-restarts"></a>Scénario : Redémarrages fréquents de regionserver

### <a name="issue"></a>Problème

Les nœuds redémarrent périodiquement. Dans les journaux regionserver, vous pouvez voir des entrées similaires à celles-ci :

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Cause :

Longue pause de nettoyage de la mémoire de machine virtuelle Java de regionserver. La pause entraîne l’absence de réponse de regionserver et l’impossibilité d’envoyer une pulsation à HMaster dans les 40 secondes de délai d’expiration de la session zk. HMaster estime que regionserver est inactif et l’abandonne, puis redémarre.

### <a name="resolution"></a>Résolution :

Modifiez le délai d’expiration de la session Zookeeper ; non seulement le paramètre hbase-site `zookeeper.session.timeout`, mais également le paramètre zookeeper zoo.cfg `maxSessionTimeout` doit être modifié.

1. Dans l’interface utilisateur Ambari, accédez à **HBase -> Configs -> Paramètres**, dans la section Délais d’expiration, modifiez la valeur du délai d’expiration de la session Zookeeper.

1. Dans l’interface utilisateur Ambari, accédez à **Zookeeper -> Configs -> Custom (Personnalisée)** zoo.cfg, puis ajoutez/modifiez le paramètre suivant. Vérifiez que la valeur est identique à hbase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Redémarrez les services requis.

---

## <a name="scenario-log-splitting-failure"></a>Scénario : Échec du fractionnement du journal

### <a name="issue"></a>Problème

HMaster n’apparaît pas sur un cluster HBase.

### <a name="cause"></a>Cause :

Paramètres HDFS et HBase mal configurés pour un compte de stockage secondaire.

### <a name="resolution"></a>Résolution :

Définissez hbase.rootdir: wasb://@.blob.core.windows.net/hbase et redémarrez les services sur Ambari.

---

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
