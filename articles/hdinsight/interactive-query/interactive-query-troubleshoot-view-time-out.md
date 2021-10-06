---
title: La vue Apache Hive expire à partir du résultat de requête - Azure HDInsight
description: La vue Apache Hive expire lors de la récupération d’un résultat de requête dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 5b1ec7fec182d5b0b6f2d68467d6e3e84fdb5f3c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601403"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scénario : La vue Apache Hive expire lors de la récupération d’un résultat de requête dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Lors de l’exécution de certaines requêtes à partir de la vue Apache Hive, l’erreur suivante peut se produire :

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Cause

La valeur du délai d’expiration par défaut de la vue Hive peut ne pas convenir à la requête que vous exécutez. La période spécifiée est trop courte pour que la vue Hive récupère le résultat de la requête.

## <a name="resolution"></a>Résolution

1. Augmentez les délais d’attente de la vue Hive Apache Ambari en définissant les propriétés suivantes dans `/etc/ambari-server/conf/ambari.properties` pour **nœuds principaux**.
  ```
  views.ambari.request.read.timeout.millis=300000
  views.request.read.timeout.millis=300000
  views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
  ```
  La valeur de `HIVE_VIEW_INSTANCE_NAME` est disponible à la fin de l’URL de la vue Hive.

2. Redémarrez le serveur Ambari actif en exécutant ce qui suit. Si vous recevez un message d’erreur indiquant qu’il ne s’agit pas du serveur Ambari actif, il vous suffit d’utiliser ssh dans le nœud principal suivant et de répéter cette étape.
  ```
  sudo ambari-server restart
  ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
