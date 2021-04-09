---
title: Connexions Apache Hive à Apache Zookeeper - Azure HDInsight
description: La vue Apache Hive est inaccessible en raison de problèmes liés à Apache Zookeeper dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939291"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Scénario : Apache Hive ne parvient pas à établir une connexion à Apache Zookeeper dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

L’affichage Hive est inaccessible, et les journaux dans `/var/log/hive` affichent une erreur semblable à la suivante :

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Cause

Il est possible que Hive ne parvienne pas à établir une connexion à Zookeeper, ce qui empêche le lancement de l’affichage Hive.

## <a name="resolution"></a>Résolution

1. Vérifiez que le service Zookeeper est sain.

1. Vérifiez si le service Zookeeper a une entrée ZNode pour Hive Server2. La valeur est manquante ou incorrecte.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Pour rétablir la connectivité, redémarrez les nœuds Zookeeper et redémarrez HiveServer2.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]