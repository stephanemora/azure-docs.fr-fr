---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 07/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b742e43d45517f36e6f43207afd0c77364a50d6c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438962"
---
### <a name="list-all-azure-arc-enabled-kubernetes-clusters-with-azure-monitor-extension"></a>Répertorier tous les clusters Kubernetes avec Azure Arc avec l’extension Azure Monitor

Renvoie l’ID de cluster connecté de chaque cluster Kubernetes avec Azure Arc et pour lequel l’extension Azure Monitor installée.

```kusto
KubernetesConfigurationResources
| where type == 'microsoft.kubernetesconfiguration/extensions'
| where properties.ExtensionType  == 'microsoft.azuremonitor.containers'
| parse id with connectedClusterId '/providers/Microsoft.KubernetesConfiguration/Extensions' *
| project connectedClusterId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "KubernetesConfigurationResources | where type == 'microsoft.kubernetesconfiguration/extensions' | where properties.ExtensionType == 'microsoft.azuremonitor.containers' | parse id with connectedClusterId '/providers/Microsoft.KubernetesConfiguration/Extensions' * | project connectedClusterId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "KubernetesConfigurationResources | where type == 'microsoft.kubernetesconfiguration/extensions' | where properties.ExtensionType == 'microsoft.azuremonitor.containers' | parse id with connectedClusterId '/providers/Microsoft.KubernetesConfiguration/Extensions' * | project connectedClusterId"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/KubernetesConfigurationResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.kubernetesconfiguration%2fextensions%27%0a%7c%20where%20properties.ExtensionType%20%20%3d%3d%20%27microsoft.azuremonitor.containers%27%0a%7c%20parse%20id%20with%20connectedClusterId%20%27%2fproviders%2fMicrosoft.KubernetesConfiguration%2fExtensions%27%20*%0a%7c%20project%20connectedClusterId" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/KubernetesConfigurationResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.kubernetesconfiguration%2fextensions%27%0a%7c%20where%20properties.ExtensionType%20%20%3d%3d%20%27microsoft.azuremonitor.containers%27%0a%7c%20parse%20id%20with%20connectedClusterId%20%27%2fproviders%2fMicrosoft.KubernetesConfiguration%2fExtensions%27%20*%0a%7c%20project%20connectedClusterId" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/KubernetesConfigurationResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.kubernetesconfiguration%2fextensions%27%0a%7c%20where%20properties.ExtensionType%20%20%3d%3d%20%27microsoft.azuremonitor.containers%27%0a%7c%20parse%20id%20with%20connectedClusterId%20%27%2fproviders%2fMicrosoft.KubernetesConfiguration%2fExtensions%27%20*%0a%7c%20project%20connectedClusterId" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension"></a>Répertorier tous les clusters Kubernetes avec Azure Arc sans extension Azure Monitor

Renvoie l’ID de cluster connecté de chaque cluster Kubernetes avec Azure Arc auquel il manque l’extension Azure Monitor.

```kusto
Resources
| where type =~ 'Microsoft.Kubernetes/connectedClusters' | extend connectedClusterId = tolower(id) | project connectedClusterId 
| join kind = leftouter
    (KubernetesConfigurationResources
    | where type == 'microsoft.kubernetesconfiguration/extensions'
    | where properties.ExtensionType  == 'microsoft.azuremonitor.containers'
    | parse tolower(id) with connectedClusterId '/providers/microsoft.kubernetesconfiguration/extensions' *
    | project connectedClusterId
)  on connectedClusterId
| where connectedClusterId1 == ''
| project connectedClusterId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Kubernetes/connectedClusters' | extend connectedClusterId = tolower(id) | project connectedClusterId | join kind = leftouter (KubernetesConfigurationResources | where type == 'microsoft.kubernetesconfiguration/extensions' | where properties.ExtensionType == 'microsoft.azuremonitor.containers' | parse tolower(id) with connectedClusterId '/providers/microsoft.kubernetesconfiguration/extensions' * | project connectedClusterId ) on connectedClusterId | where connectedClusterId1 == '' | project connectedClusterId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Kubernetes/connectedClusters' | extend connectedClusterId = tolower(id) | project connectedClusterId | join kind = leftouter (KubernetesConfigurationResources | where type == 'microsoft.kubernetesconfiguration/extensions' | where properties.ExtensionType == 'microsoft.azuremonitor.containers' | parse tolower(id) with connectedClusterId '/providers/microsoft.kubernetesconfiguration/extensions' * | project connectedClusterId ) on connectedClusterId | where connectedClusterId1 == '' | project connectedClusterId"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Kubernetes%2fconnectedClusters%27%20%7c%20extend%20connectedClusterId%20%3d%20tolower(id)%20%7c%20project%20connectedClusterId%20%0a%7c%20join%20kind%20%3d%20leftouter%0a%09(KubernetesConfigurationResources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.kubernetesconfiguration%2fextensions%27%0a%09%7c%20where%20properties.ExtensionType%20%20%3d%3d%20%27microsoft.azuremonitor.containers%27%0a%09%7c%20parse%20tolower(id)%20with%20connectedClusterId%20%27%2fproviders%2fmicrosoft.kubernetesconfiguration%2fextensions%27%20*%0a%09%7c%20project%20connectedClusterId%0a)%20%20on%20connectedClusterId%0a%7c%20where%20connectedClusterId1%20%3d%3d%20%27%27%0a%7c%20project%20connectedClusterId" target="_blank">portal.azure.com</a>
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Kubernetes%2fconnectedClusters%27%20%7c%20extend%20connectedClusterId%20%3d%20tolower(id)%20%7c%20project%20connectedClusterId%20%0a%7c%20join%20kind%20%3d%20leftouter%0a%09(KubernetesConfigurationResources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.kubernetesconfiguration%2fextensions%27%0a%09%7c%20where%20properties.ExtensionType%20%20%3d%3d%20%27microsoft.azuremonitor.containers%27%0a%09%7c%20parse%20tolower(id)%20with%20connectedClusterId%20%27%2fproviders%2fmicrosoft.kubernetesconfiguration%2fextensions%27%20*%0a%09%7c%20project%20connectedClusterId%0a)%20%20on%20connectedClusterId%0a%7c%20where%20connectedClusterId1%20%3d%3d%20%27%27%0a%7c%20project%20connectedClusterId" target="_blank">portal.azure.us</a>
- Portail Azure China 21Vianet : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Kubernetes%2fconnectedClusters%27%20%7c%20extend%20connectedClusterId%20%3d%20tolower(id)%20%7c%20project%20connectedClusterId%20%0a%7c%20join%20kind%20%3d%20leftouter%0a%09(KubernetesConfigurationResources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.kubernetesconfiguration%2fextensions%27%0a%09%7c%20where%20properties.ExtensionType%20%20%3d%3d%20%27microsoft.azuremonitor.containers%27%0a%09%7c%20parse%20tolower(id)%20with%20connectedClusterId%20%27%2fproviders%2fmicrosoft.kubernetesconfiguration%2fextensions%27%20*%0a%09%7c%20project%20connectedClusterId%0a)%20%20on%20connectedClusterId%0a%7c%20where%20connectedClusterId1%20%3d%3d%20%27%27%0a%7c%20project%20connectedClusterId" target="_blank">portal.azure.cn</a>

---

