---
title: HDFS local bloqué en mode sans échec sur le cluster Azure HDInsight
description: Dépanner une instance locale d’Apache HDFS lorsqu’elle est bloquée en mode sans échec sur un cluster Apache dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 800986b0e50f5e674929e3ad10a52c419494681a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533663"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Scénario : HDFS local bloqué en mode sans échec sur le cluster Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Le stockage Apache HDFS (Hadoop Distributed File System) est bloqué en mode sans échec sur le cluster HDInsight. Vous recevez un message d’erreur semblable à ce qui suit :

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Cause

Le cluster HDInsight a été redimensionné avec très peu de nœuds, ou le nombre de nœuds est proche du facteur de réplication HDFS.

## <a name="resolution"></a>Résolution

1. Créez un rapport sur l’état du stockage HDFS du cluster HDInsight à l’aide des commandes suivantes :

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Vérifiez l’intégrité du stockage HDFS du cluster HDInsight à l’aide des commandes suivantes :

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Si aucun bloc n’est manquant, endommagé ou sous-répliqué ou si ces blocs peuvent être ignorés, exécutez la commande suivante pour désactiver le mode sans échec sur le nœud de nom :

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support** . Pour plus d’informations, consultez [Création d’une demande de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).