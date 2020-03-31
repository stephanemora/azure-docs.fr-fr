---
title: Exemples de requêtes de démarrage
description: Utilisez Azure Resource Graph pour exécuter certaines requêtes de démarrage, notamment compter des ressources ou les trier, par exemple selon une étiquette spécifique.
ms.date: 11/21/2019
ms.topic: sample
ms.openlocfilehash: b966d8c239cb6ff706c967174bcea23bf25de374
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79221966"
---
# <a name="starter-resource-graph-query-samples"></a>Exemples de requêtes Resource Graph de démarrage

Pour comprendre comment fonctionnent les requêtes dans Azure Resource Graph, vous devez au préalable vous familiariser avec le [langage de requête](../concepts/query-language.md). Si vous ne connaissez pas bien le [langage de requête Kusto (KQL)](/azure/kusto/query/index), nous vous recommandons de passer en revue le [tutoriel KQL](/azure/kusto/query/tutorial) pour apprendre à composer des requêtes pour les ressources qui vous intéressent.

Nous allons vous guider tout au long des requêtes de démarrage suivantes :

> [!div class="checklist"]
> - [Compter les ressources Azure](#count-resources)
> - [Compter les ressources de coffre de clés](#count-keyvaults)
> - [Lister les ressources triées par nom](#list-resources)
> - [Afficher toutes les machines virtuelles classées par nom dans l’ordre décroissant](#show-vms)
> - [Afficher les cinq premières machines virtuelles par nom et leur type de système d’exploitation](#show-sorted)
> - [Compter les machines virtuelles par type de système d’exploitation](#count-os)
> - [Afficher les ressources contenant storage](#show-storage)
> - [Lister toutes les adresses IP publiques](#list-publicip)
> - [Compter les ressources avec des adresses IP configurées par abonnement](#count-resources-by-ip)
> - [Lister les ressources avec une valeur d’étiquette spécifique](#list-tag)
> - [Lister tous les comptes de stockage avec une valeur d’étiquette spécifique](#list-specific-tag)
> - [Afficher des alias pour une ressource de machine virtuelle](#show-aliases)
> - [Afficher des valeurs distinctes pour un alias spécifique](#distinct-alias-values)
> - [Afficher les groupes de sécurité réseau qui ne sont pas associés](#unassociated-nsgs)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="language-support"></a>Support multilingue

Azure CLI (par le biais d’une extension) et Azure PowerShell (par le biais d’un module) prennent en charge Azure Resource Graph. Avant d’exécuter les requêtes suivantes, vérifiez que votre environnement est prêt. Consultez [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) et [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pour savoir comment installer et valider l’environnement de votre interpréteur de commandes.

## <a name="count-azure-resources"></a><a name="count-resources" />Compter les ressources Azure

Cette requête retourne le nombre de ressources Azure présentes dans les abonnements auxquels vous avez accès. Elle permet également de valider que votre interpréteur de commandes dispose des composants Azure Resource Graph appropriés et en état de fonctionnement.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults" />Compter les ressources de coffre de clés

Cette requête utilise `count` au lieu de `summarize` pour compter le nombre d’enregistrements retournés. Seuls les coffres de clés sont inclus dans le décompte.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources" />Lister les ressources triées par nom

Cette requête renvoie n’importe quel type de ressource, mais uniquement les propriétés de **nom**, de **type**, et d’**emplacement**. Elle utilise `order by` pour trier les propriétés par **nom** dans l’ordre croissant (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Afficher toutes les machines virtuelles classées par nom dans l’ordre décroissant

Pour répertorier uniquement les machines virtuelles (qui sont de type `Microsoft.Compute/virtualMachines`), vous pouvez utiliser la propriété **type** dans les résultats. Comme dans la requête précédente, `order by` indique l’ordre de tri (`desc` spécifie un ordre décroissant). Lors de la mise en correspondance du type, `=~` indique à Resource Graph de ne pas respecter la casse.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />Afficher les cinq premières machines virtuelles par nom et leur type de système d’exploitation

Cette requête utilise `top` pour récupérer uniquement les cinq enregistrements correspondants classés par nom. Le type de ressource Azure est `Microsoft.Compute/virtualMachines`. `project` indique à Azure Resource Graph les propriétés à inclure.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os" />Compter les machines virtuelles par type de système d’exploitation

Partons de la requête précédente. Nous limitons toujours les ressources Azure à celles de type `Microsoft.Compute/virtualMachines`, mais nous ne limitons plus le nombre d’enregistrements retournés.
Au lieu de cela, nous utilisons `summarize` et `count()` pour définir comment regrouper et agréger les valeurs par propriété (`properties.storageProfile.osDisk.osType` dans cet exemple). Pour voir à quoi ressemble cette chaîne dans l’objet complet, consultez [Explorer les ressources - Découverte de machines virtuelles](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

Il est possible d’écrire différemment la même requête en étendant (`extend`) une propriété et en lui donnant un nom temporaire pour une utilisation au sein de la requête (**os** dans ce cas). **os** est ensuite utilisé par `summarize` et `count()`, comme dans l’exemple précédent.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

> [!NOTE]
> Même si `=~` autorise la mise en correspondance sans respect de la casse, sachez que l’utilisation de propriétés comme **properties.storageProfile.osDisk.osType** dans la requête nécessite le respect de la casse. Si la casse de la propriété est incorrecte, une valeur null ou incorrecte est retournée et la totalisation ou le regroupement est erroné.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage" />Afficher les ressources contenant du stockage

Au lieu de définir explicitement le type à mettre en correspondance, cet exemple de requête trouve n’importe quelle ressource Azure contenant (`contains`) le mot **storage**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip" />Lister toutes les adresses IP publiques

Comme dans la requête précédente, nous recherchons toutes les ressources dont le type contient un mot (ici, **publicIPAddresses**).
Cette requête va plus loin en incluant uniquement les résultats où **properties.ipAddress**
 a la valeur `isnotempty`, en retournant uniquement **properties.ipAddress** et en limitant (`limit`) les résultats aux 100 premiers.
100. Vous devrez peut-être placer les guillemets dans une séquence d’échappement en fonction de votre interpréteur de commandes.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />Compter les ressources ayant des adresses IP configurées par abonnement

Reprenons l’exemple de requête précédent et ajoutons `summarize` et `count()` pour obtenir une liste par abonnement des ressources avec des adresses IP configurées.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag" />Lister les ressources avec une valeur de balise spécifique

Nous pouvons limiter les résultats en fonction d’autres propriétés que le type de ressource Azure, notamment une étiquette. Dans cet exemple, nous filtrons les ressources Azure ayant une étiquette nommée **environment** avec pour valeur **internal**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

Pour savoir également les étiquettes dont dispose la ressource et leurs valeurs, ajoutez la propriété **étiquettes** au mot-clé `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />Lister tous les comptes de stockage avec une valeur de balise spécifique

Combinez la fonctionnalité de filtre de l’exemple précédent, et filtrez le type de ressource Azure en utilisant la propriété **type**. Cette requête limite également notre recherche à certains types de ressources Azure, qui disposent d’un nom d’étiquette et d’une valeur spécifiques.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

> [!NOTE]
> Cet exemple utilise une condition `==` pour la mise en correspondance au lieu de `=~`. `==` indique que la mise en correspondance respecte la casse.

## <a name="show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />Afficher des alias pour une ressource de machine virtuelle

Des [alias Azure Policy](../../policy/concepts/definition-structure.md#aliases) sont utilisés par Azure Policy pour gérer la conformité des ressources. Azure Resource Graph peut restituer les _alias_ d’un type de ressource. Ces valeurs sont utiles pour comparer la valeur actuelle des alias au moment de la création d’une définition de stratégie personnalisée. Le tableau des _alias_ n’est pas fourni par défaut dans les résultats d’une requête. Utilisez `project aliases` pour l’ajouter explicitement aux résultats.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />Afficher des valeurs distinctes pour un alias spécifique

Voir la valeur des alias sur une ressource unique est utile, mais cela ne montre pas la valeur réelle de l’utilisation d’Azure Resource Graph pour interroger différents abonnements. Cet exemple passe en revue l’ensemble des valeurs d’un alias spécifique et restitue les valeurs distinctes.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />Afficher les groupes de sécurité réseau qui ne sont pas associés

Cette requête retourne les groupes de sécurité réseau (NSG) qui ne sont pas associés à une interface réseau ou à un sous-réseau.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

![Icône de l’Explorateur Resource Graph](../media/resource-graph-small.png) Essayez cette requête dans l’Explorateur Azure Resource Graph :

- Portail Azure : <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Government : <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)
- Portail Azure Chine : <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Icône Ouvrir le lien dans une nouvelle fenêtre](../../media/new-window.png)

---

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détails le [langage de requête](../concepts/query-language.md).
- Découvrez plus en détails comment [explorer des ressources](../concepts/explore-resources.md).
- Consulter des exemples de [requêtes avancées](advanced.md).