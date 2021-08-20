---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 07/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 62b17a4c8f44863074b530dfd3387d1a23323b26
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457103"
---
### <a name="get-enabled-resource-types-for-azure-arc-enabled-custom-locations"></a>Obtenir les types de ressources activés pour les emplacements personnalisés compatibles avec Azure Arc

Fournit une liste des types de ressources activés pour les emplacements personnalisés compatibles avec Azure Arc.

```kusto
ExtendedLocationResources
| where type == 'microsoft.extendedlocation/customlocations/enabledresourcetypes'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ExtendedLocationResources | where type == 'microsoft.extendedlocation/customlocations/enabledresourcetypes'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ExtendedLocationResources | where type == 'microsoft.extendedlocation/customlocations/enabledresourcetypes'"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ExtendedLocationResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.extendedlocation%2fcustomlocations%2fenabledresourcetypes%27" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ExtendedLocationResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.extendedlocation%2fcustomlocations%2fenabledresourcetypes%27" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ExtendedLocationResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.extendedlocation%2fcustomlocations%2fenabledresourcetypes%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled"></a>Répertorier les emplacements personnalisés compatibles avec Azure Arc avec activation de VMware ou SCVMM

Fournit la liste de tous les emplacements personnalisés compatibles avec Azure Arc dont les types de ressources VMware ou SCVMM sont activés.

```kusto
Resources
| where type =~ 'microsoft.extendedlocation/customlocations' and properties.provisioningState =~ 'succeeded'
| extend clusterExtensionIds=properties.clusterExtensionIds
| mvexpand clusterExtensionIds
| extend clusterExtensionId = tolower(clusterExtensionIds)
| join kind=leftouter(
    ExtendedLocationResources
    | where type =~ 'microsoft.extendedlocation/customLocations/enabledResourcetypes'
    | project clusterExtensionId = tolower(properties.clusterExtensionId), extensionType = tolower(properties.extensionType)
    | where extensionType in~ ('microsoft.scvmm','microsoft.vmware')
) on clusterExtensionId
| where extensionType in~ ('microsoft.scvmm','microsoft.vmware')
| summarize virtualMachineKindsEnabled=make_set(extensionType) by id,name,location
| sort by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.extendedlocation/customlocations' and properties.provisioningState =~ 'succeeded' | extend clusterExtensionIds=properties.clusterExtensionIds | mvexpand clusterExtensionIds | extend clusterExtensionId = tolower(clusterExtensionIds) | join kind=leftouter( ExtendedLocationResources | where type =~ 'microsoft.extendedlocation/customLocations/enabledResourcetypes' | project clusterExtensionId = tolower(properties.clusterExtensionId), extensionType = tolower(properties.extensionType) | where extensionType in~ ('microsoft.scvmm','microsoft.vmware') ) on clusterExtensionId | where extensionType in~ ('microsoft.scvmm','microsoft.vmware') | summarize virtualMachineKindsEnabled=make_set(extensionType) by id,name,location | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.extendedlocation/customlocations' and properties.provisioningState =~ 'succeeded' | extend clusterExtensionIds=properties.clusterExtensionIds | mvexpand clusterExtensionIds | extend clusterExtensionId = tolower(clusterExtensionIds) | join kind=leftouter( ExtendedLocationResources | where type =~ 'microsoft.extendedlocation/customLocations/enabledResourcetypes' | project clusterExtensionId = tolower(properties.clusterExtensionId), extensionType = tolower(properties.extensionType) | where extensionType in~ ('microsoft.scvmm','microsoft.vmware') ) on clusterExtensionId | where extensionType in~ ('microsoft.scvmm','microsoft.vmware') | summarize virtualMachineKindsEnabled=make_set(extensionType) by id,name,location | sort by name asc"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.extendedlocation%2fcustomlocations%27%20and%20properties.provisioningState%20%3d%7e%20%27succeeded%27%0a%7c%20extend%20clusterExtensionIds%3dproperties.clusterExtensionIds%0a%7c%20mvexpand%20clusterExtensionIds%0a%7c%20extend%20clusterExtensionId%20%3d%20tolower(clusterExtensionIds)%0a%7c%20join%20kind%3dleftouter(%0a%09ExtendedLocationResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.extendedlocation%2fcustomLocations%2fenabledResourcetypes%27%0a%09%7c%20project%20clusterExtensionId%20%3d%20tolower(properties.clusterExtensionId)%2c%20extensionType%20%3d%20tolower(properties.extensionType)%0a%09%7c%20where%20extensionType%20in%7e%20(%27microsoft.scvmm%27%2c%27microsoft.vmware%27)%0a)%20on%20clusterExtensionId%0a%7c%20where%20extensionType%20in%7e%20(%27microsoft.scvmm%27%2c%27microsoft.vmware%27)%0a%7c%20summarize%20virtualMachineKindsEnabled%3dmake_set(extensionType)%20by%20id%2cname%2clocation%0a%7c%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.extendedlocation%2fcustomlocations%27%20and%20properties.provisioningState%20%3d%7e%20%27succeeded%27%0a%7c%20extend%20clusterExtensionIds%3dproperties.clusterExtensionIds%0a%7c%20mvexpand%20clusterExtensionIds%0a%7c%20extend%20clusterExtensionId%20%3d%20tolower(clusterExtensionIds)%0a%7c%20join%20kind%3dleftouter(%0a%09ExtendedLocationResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.extendedlocation%2fcustomLocations%2fenabledResourcetypes%27%0a%09%7c%20project%20clusterExtensionId%20%3d%20tolower(properties.clusterExtensionId)%2c%20extensionType%20%3d%20tolower(properties.extensionType)%0a%09%7c%20where%20extensionType%20in%7e%20(%27microsoft.scvmm%27%2c%27microsoft.vmware%27)%0a)%20on%20clusterExtensionId%0a%7c%20where%20extensionType%20in%7e%20(%27microsoft.scvmm%27%2c%27microsoft.vmware%27)%0a%7c%20summarize%20virtualMachineKindsEnabled%3dmake_set(extensionType)%20by%20id%2cname%2clocation%0a%7c%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.extendedlocation%2fcustomlocations%27%20and%20properties.provisioningState%20%3d%7e%20%27succeeded%27%0a%7c%20extend%20clusterExtensionIds%3dproperties.clusterExtensionIds%0a%7c%20mvexpand%20clusterExtensionIds%0a%7c%20extend%20clusterExtensionId%20%3d%20tolower(clusterExtensionIds)%0a%7c%20join%20kind%3dleftouter(%0a%09ExtendedLocationResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.extendedlocation%2fcustomLocations%2fenabledResourcetypes%27%0a%09%7c%20project%20clusterExtensionId%20%3d%20tolower(properties.clusterExtensionId)%2c%20extensionType%20%3d%20tolower(properties.extensionType)%0a%09%7c%20where%20extensionType%20in%7e%20(%27microsoft.scvmm%27%2c%27microsoft.vmware%27)%0a)%20on%20clusterExtensionId%0a%7c%20where%20extensionType%20in%7e%20(%27microsoft.scvmm%27%2c%27microsoft.vmware%27)%0a%7c%20summarize%20virtualMachineKindsEnabled%3dmake_set(extensionType)%20by%20id%2cname%2clocation%0a%7c%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

