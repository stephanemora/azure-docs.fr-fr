---
title: Problèmes de connectivité Apache Phoenix dans Azure HDInsight
description: Problèmes de connectivité Apache Phoenix dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886584"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scénario : Problèmes de connectivité Apache Phoenix dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Impossible de se connecter à Apache HBase avec Apache Phoenix. Les raisons peuvent varier.

## <a name="cause-incorrect-ip"></a>Cause : IP incorrecte

Adresse IP incorrecte du nœud Zookeeper actif.

### <a name="resolution"></a>Résolution :

L’adresse IP du nœud Zookeeper actif peut être identifiée à partir de l’interface utilisateur Ambari, en suivant les liens vers **HBase -> Liens rapides -> ZK***  **(actif) -> Zookeeper Info (Informations Zookeeper)** . Corrigez si nécessaire.

---

## <a name="cause-systemcatalog-table-offline"></a>Cause : Table SYSTEM.CATALOG hors connexion

Lors de l’exécution de commandes telles que `!tables`, vous recevez un message d’erreur semblable à celui-ci :

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Lors de l’exécution de commandes telles que `count 'SYSTEM.CATALOG'`, vous recevez un message d’erreur semblable à celui-ci :

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Résolution :

Redémarrez le service HMaster sur tous les nœuds Zookeeper à partir de l’interface utilisateur Ambari.

1. Accédez au lien **HBase -> Active HBase Master** (HBase -> HBase Master actif) dans la section Summary (Résumé) de HBase.

1. Dans la section **Components** (Composants), redémarrez le service HBase Master.

1. Répétez les étapes ci-dessus pour les services **Standby HBase Master** (HBase Master de secours) restants.

La stabilisation et la récupération complète du service HBase Master peuvent prendre jusqu’à 5 minutes. Une fois la table `SYSTEM.CATALOG` revenue à son fonctionnement normal, le problème de connectivité à Apache Phoenix devrait se résoudre automatiquement.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
