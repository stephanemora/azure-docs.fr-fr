---
title: Redémarrer des machines virtuelles pour le cluster Azure HDInsight
description: Découvrez comment redémarrer des machines virtuelles qui ne répondent pas pour un cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077012"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Redémarrer des machines virtuelles pour le cluster HDInsight

Les clusters HDInsight contiennent des groupes de machines virtuelles en tant que nœuds de cluster. Pour les clusters exécutés pour une longue durée, ces nœuds peuvent cesser de répondre pour différentes raisons. Cet article explique comment redémarrer des machines virtuelles qui ne répondent pas dans un cluster HDInsight.

## <a name="when-to-reboot"></a>Quand redémarrer

> [!WARNING]  
> Le redémarrage des machines virtuelles dans le cluster entraîne un temps d’arrêt du nœud et le redémarrage des services sur le nœud. 

Pendant le redémarrage d’un nœud, le cluster peut devenir défectueux, les tâches peuvent ralentir ou échouer. Si vous essayez de redémarrer le nœud principal actif, tous les travaux en cours d’exécution sont supprimés et vous ne pouvez pas soumettre des travaux au cluster tant que les services ne sont pas à nouveau opérationnels. Vous devez redémarrer les machines virtuelles uniquement lorsque cela est nécessaire. Voici quelques conseils pour savoir quand envisager le redémarrage des machines virtuelles.

- Vous ne pouvez pas exécuter SSH dans le nœud, mais il répond aux tests ping.
- Le nœud worker est en cours d’inversion sans pulsation dans l’interface utilisateur Ambari.
- Le disque temporaire est plein sur le nœud.
- Le tableau de processus sur la machine virtuelle a de nombreuses entrées pour lesquelles le processus est terminé et qui sont répertoriées sous « État terminé ».

> [!WARNING]  
> Vous devez être très prudent lorsque vous redémarrez des machines virtuelles pour les clusters **HBase** et **Kafka**, car cela peut entraîner la perte de données.

## <a name="use-powershell-to-reboot-vms"></a>Utiliser PowerShell pour redémarrer des machines virtuelles

Deux étapes sont nécessaires pour utiliser l’opération de redémarrage de nœud : répertorier les nœuds et les redémarrer.

1. Répertorier les nœuds. Vous pouvez récupérer la liste des nœuds de cluster via [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Redémarrez les ordinateurs hôtes. Après avoir obtenu le nom des nœuds que vous souhaitez redémarrer, redémarrez-les à l’aide de [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Utiliser l’API REST pour redémarrer les machines virtuelles

Vous pouvez utiliser la fonctionnalité **Try it** dans la documentation de l’API pour envoyer des requêtes à HDInsight. Deux étapes sont nécessaires pour utiliser l’opération de redémarrage de nœud : répertorier les nœuds et les redémarrer.

1. Répertorier les nœuds. Vous pouvez récupérer la liste des nœuds de cluster à partir de l’API REST ou de Ambari. Pour plus d’informations, consultez [HDInsight liste les hôtes de l’opération de l’API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Redémarrez les ordinateurs hôtes. Après avoir obtenu les noms des nœuds que vous souhaitez redémarrer, utilisez l’API REST pour redémarrer les nœuds. Le nom du nœud suit le modèle de **« NodeType(wn/hn/zk/gw) » + « x » + « six premiers caractères du nom du cluster »** . Pour plus d’informations, consultez [HDInsight redémarre les hôtes de l’opération de l’API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Les noms réels des nœuds que vous souhaitez redémarrer sont spécifiés dans un tableau JSON dans le corps de la requête.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Étapes suivantes

* [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [API REST des machines virtuelles HDInsight](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [API REST de HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)
