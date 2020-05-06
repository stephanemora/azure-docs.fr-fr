---
title: Le serveur Apache ZooKeeper ne parvient pas à former un quorum dans Azure HDInsight
description: Le serveur Apache ZooKeeper ne parvient pas à former un quorum dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 41ac109e5c5379e6085dd57a3fcd8119915558fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133271"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Le serveur Apache ZooKeeper ne parvient pas à former un quorum dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Le serveur Apache ZooKeeper n’est pas sain, ce qui peut se caractériser par les symptômes suivants : les nœuds Resource Managers/de nom sont en mode veille, les opérations HDFS simples ne fonctionnent pas, `zkFailoverController` est arrêté et ne peut pas être démarré, les travaux Yarn/Spark/Livy échouent en raison d’erreurs Zookeeper. Le démarrage des démons LLAP peut également échouer sur des clusters Interactive Hive ou Secure Spark. Vous pouvez recevoir un message d’erreur similaire au suivant :

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

Dans les journaux de serveur Zookeeper sur tout hôte Zookeeper à l’emplacement /var/log/zookeeper/zookeeper-zookeeper-server-\*.out, vous pouvez également voir l’erreur suivante :

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Cause

Lorsque le volume de fichiers d’instantanés est important ou que les fichiers d’instantanés sont endommagés, le serveur ZooKeeper ne parvient pas à former un quorum, ce qui entraîne la non-intégrité des services liés à ZooKeeper. Le serveur ZooKeeper ne supprimera pas les anciens fichiers instantanés de son répertoire de données, il s'agit plutôt d'une tâche périodique que les utilisateurs doivent effectuer pour maintenir l’intégrité de ZooKeeper. Pour en savoir plus, consultez [Atouts et limitations de ZooKeeper](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Résolution

Vérifiez le répertoire de données de ZooKeeper `/hadoop/zookeeper/version-2` et `/hadoop/hdinsight-zookeeper/version-2` pour savoir si le fichier des captures instantanées est volumineux. En présence de captures instantanées volumineuses, procédez comme suit :

1. Vérifiez l’état des autres serveurs ZooKeeper dans le même quorum pour vous assurer qu’ils fonctionnent correctement avec la commande « `echo stat | nc {zk_host_ip} 2181 (or 2182)` ».  

1. Connectez-vous à l’hôte ZooKeeper problématique, sauvegardez les instantanés et les journaux des transactions dans `/hadoop/zookeeper/version-2` et `/hadoop/hdinsight-zookeeper/version-2`, puis nettoyez ces fichiers dans les deux répertoires. 

1. Redémarrez le serveur ZooKeeper problématique dans Ambari ou l’hôte ZooKeeper. Ensuite, redémarrez le service qui rencontre des problèmes.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

- Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

- Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

- Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
