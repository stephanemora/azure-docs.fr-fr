---
title: Créer un pool dans des zones de disponibilité
description: Découvrez comment créer un pool Batch avec une stratégie zonale pour vous protéger contre les défaillances.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725286"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Créer un pool Azure Batch dans des zones de disponibilité

Les régions Azure qui prennent en charge les [zones de disponibilité](https://azure.microsoft.com/global-infrastructure/availability-zones/) comportent au minimum trois zones distinctes, chacune avec leurs propres source d’alimentation, réseau et système de refroidissement. Lorsque vous créez un pool Azure Batch à l’aide de la configuration de machine virtuelle, vous pouvez choisir d’approvisionner votre pool Batch dans les zones de disponibilité. La création de votre pool avec cette stratégie zonale vous aide à protéger vos nœuds de calcul Batch contre les défaillances au niveau du centre de données Azure.

Par exemple, vous pouvez créer votre pool avec une stratégie zonale dans une région Azure qui prend en charge trois zones de disponibilité. Si un centre de données Azure dans l’une des zones de disponibilité rencontre une défaillance de l’infrastructure, votre pool Batch disposera toujours de nœuds sains dans les deux autres zones de disponibilité. Le pool restera donc disponible pour la planification des tâches.

## <a name="regional-support-and-other-requirements"></a>Prise en charge régionale et autres exigences

Batch maintient la parité avec Azure pour la prise en charge de zones de disponibilité. Pour utiliser l’option zonale, votre pool doit être créé dans une région [Azure prise en charge](../availability-zones/az-region.md).

Pour que votre pool Batch soit alloué dans les différentes zones de disponibilité, la région Azure dans laquelle le pool est créé doit prendre en charge la référence SKU de machine virtuelle requise dans plusieurs zones. Vous pouvez valider cette exigence en appelant l’[API de liste des références SKU de ressource](/rest/api/compute/resourceskus/list) et vérifier le champ **locationInfo** de [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Assurez-vous que plus d’une zone est prise en charge pour la référence SKU de machine virtuelle requise.

Pour les [comptes Batch en mode abonnement utilisateur](accounts.md#batch-accounts), assurez-vous que l’abonnement dans lequel vous créez votre pool n’a pas de restriction d’offre de zone sur la référence SKU de machine virtuelle requise. Pour confirmer cela, appelez l’[API de liste des références SKU des ressources](/rest/api/compute/resourceskus/list) et vérifiez [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Si une restriction de zone existe, vous pouvez envoyer un [ticket de support](/troubleshoot/azure/general/region-access-request-process) pour supprimer la restriction de zone.

Notez également que vous ne pouvez pas créer un pool avec une stratégie zonale si la communication entre les nœuds est activée et utilise une [référence SKU de machine virtuelle qui prend en charge InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Créer un pool Batch dans des zones de disponibilité

Les exemples suivants montrent comment créer un pool Batch dans des zones de disponibilité.

> [!NOTE]
> Quand vous créez votre pool avec une stratégie zonale, le service Batch essaie d’allouer votre pool dans toutes les zones de disponibilité de la région sélectionnée. Vous ne pouvez pas spécifier d’allocation spécifique entre les zones.

### <a name="batch-management-client-net-sdk"></a>Kit SDK de client Batch Management .NET

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>API REST Batch

URL d’API REST

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corps de la demande

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur le [workflow et les ressources principales du service Batch](batch-service-workflow-features.md) telles que les pools, les nœuds, les travaux et les tâches.
- Apprenez-en davantage sur la [création d’un pool sur un sous-réseau d’un réseau virtuel Azure](batch-virtual-network.md).
- Découvrez comment [créer un pool Azure Batch sans adresses IP publiques](./batch-pool-no-public-ip-address.md).