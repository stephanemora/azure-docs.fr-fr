---
title: Exemples de requêtes de démarrage
description: Utilisez Azure Resource Graph pour exécuter certaines requêtes de démarrage, notamment compter des ressources ou les trier, par exemple selon une étiquette spécifique.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ba48e2a21bdee0c5698bdfa314dd3bf462c1c7e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267767"
---
# <a name="starter-resource-graph-queries"></a>Requêtes Resource Graph de démarrage

Pour comprendre comment fonctionnent les requêtes dans Azure Resource Graph, vous devez au préalable vous familiariser avec le [langage de requête](../concepts/query-language.md). Si vous ne connaissez pas bien l’[Explorateur de données Azure](../../../data-explorer/data-explorer-overview.md), nous vous recommandons de passer en revue les notions de base de ce langage pour apprendre à composer des requêtes pour les ressources qui vous intéressent.

Nous allons vous guider tout au long des requêtes de démarrage suivantes :

> [!div class="checklist"]
> - [Compter les ressources Azure](#count-resources)
> - [Lister les ressources triées par nom](#list-resources)
> - [Afficher toutes les machines virtuelles classées par nom dans l’ordre décroissant](#show-vms)
> - [Afficher les cinq premières machines virtuelles par nom et leur type de système d’exploitation](#show-sorted)
> - [Compter les machines virtuelles par type de système d’exploitation](#count-os)
> - [Afficher les ressources contenant storage](#show-storage)
> - [Lister toutes les adresses IP publiques](#list-publicip)
> - [Compter les ressources avec des adresses IP configurées par abonnement](#count-resources-by-ip)
> - [Lister les ressources avec une valeur d’étiquette spécifique](#list-tag)
> - [Lister tous les comptes de stockage avec une valeur d’étiquette spécifique](#list-specific-tag)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Support multilingue

Azure CLI (par le biais d’une extension) et Azure PowerShell (par le biais d’un module) prennent en charge Azure Resource Graph. Avant d’exécuter les requêtes suivantes, vérifiez que votre environnement est prêt. Consultez [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) et [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pour savoir comment installer et valider l’environnement de votre interpréteur de commandes.

## <a name="count-resources"></a>Compter les ressources Azure

Cette requête retourne le nombre de ressources Azure présentes dans les abonnements auxquels vous avez accès. Elle permet également de valider que votre interpréteur de commandes dispose des composants Azure Resource Graph appropriés et en état de fonctionnement.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Lister les ressources triées par nom

Cette requête renvoie n’importe quel type de ressource, mais uniquement les propriétés de **nom**, de **type**, et d’**emplacement**. Elle utilise `order by` pour trier les propriétés par **nom** dans l’ordre croissant (`asc`).

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Afficher toutes les machines virtuelles classées par nom dans l’ordre décroissant

Pour répertorier uniquement les machines virtuelles (qui sont de type `Microsoft.Compute/virtualMachines`), vous pouvez utiliser la propriété **type** dans les résultats. Comme dans la requête précédente, `order by` indique l’ordre de tri (`desc` spécifie un ordre décroissant). Lors de la mise en correspondance du type, `=~` indique à Resource Graph de ne pas respecter la casse.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>Afficher les cinq premières machines virtuelles par nom et leur type de système d’exploitation

Cette requête utilise `limit` pour récupérer uniquement les cinq enregistrements correspondants classés par nom. Le type de ressource Azure est `Microsoft.Compute/virtualMachines`. `project` indique à Azure Resource Graph les propriétés à inclure.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>Compter les machines virtuelles par type de système d’exploitation

Partons de la requête précédente. Nous limitons toujours les ressources Azure à celles de type `Microsoft.Compute/virtualMachines`, mais nous ne limitons plus le nombre d’enregistrements retournés.
Au lieu de cela, nous utilisons `summarize` et `count()` pour définir comment regrouper et agréger les valeurs par propriété (`properties.storageProfile.osDisk.osType` dans cet exemple). Pour voir à quoi ressemble cette chaîne dans l’objet complet, consultez [Explorer les ressources - Découverte de machines virtuelles](../concepts/explore-resources.md#virtual-machine-discovery).

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Il est possible d’écrire différemment la même requête en étendant (`extend`) une propriété et en lui donnant un nom temporaire pour une utilisation au sein de la requête (**os** dans ce cas). **os** est ensuite utilisé par `summarize` et `count()`, comme dans l’exemple précédent.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Même si `=~` autorise la mise en correspondance sans respect de la casse, sachez que l’utilisation de propriétés comme **properties.storageProfile.osDisk.osType** dans la requête nécessite le respect de la casse. Une valeur peut toujours être retournée si la casse de la propriété est incorrecte, mais la totalisation ou le regroupement serait erroné.

## <a name="show-storage"></a>Afficher les ressources contenant storage

Au lieu de définir explicitement le type à mettre en correspondance, cet exemple de requête trouve n’importe quelle ressource Azure contenant (`contains`) le mot **storage**.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Lister toutes les adresses IP publiques

Comme dans la requête précédente, nous recherchons toutes les ressources dont le type contient un mot (ici, **publicIPAddresses**).
Cette requête va plus loin en incluant uniquement les résultats où **properties.ipAddress**
 a la valeur `isnotempty`, en retournant uniquement **properties.ipAddress** et en limitant (`limit`) les résultats aux 100 premiers.
100. Vous devrez peut-être placer les guillemets dans une séquence d’échappement en fonction de votre interpréteur de commandes.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>Compter les ressources avec des adresses IP configurées par abonnement

Reprenons l’exemple de requête précédent et ajoutons `summarize` et `count()` pour obtenir une liste par abonnement des ressources avec des adresses IP configurées.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>Lister les ressources avec une valeur d’étiquette spécifique

Nous pouvons limiter les résultats en fonction d’autres propriétés que le type de ressource Azure, notamment une étiquette. Dans cet exemple, nous filtrons les ressources Azure ayant une étiquette nommée **environment** avec pour valeur **internal**.

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Pour savoir également les étiquettes dont dispose la ressource et leurs valeurs, ajoutez la propriété **étiquettes** au mot-clé `project`.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Lister tous les comptes de stockage avec une valeur d’étiquette spécifique

Combinez la fonctionnalité de filtre de l’exemple précédent, et filtrez le type de ressource Azure en utilisant la propriété **type**. Cette requête limite également notre recherche à certains types de ressources Azure, qui disposent d’un nom d’étiquette et d’une valeur spécifiques.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Cet exemple utilise une condition `==` pour la mise en correspondance au lieu de `=~`. `==` indique que la mise en correspondance respecte la casse.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [langage de requête](../concepts/query-language.md)
- Apprendre à [explorer les ressources](../concepts/explore-resources.md)
- Consulter des exemples de [requêtes avancées](advanced.md)