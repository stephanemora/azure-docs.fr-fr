---
title: Requêtes Azure Resource Graph avancées
description: Utilisez Azure Resource Graph pour exécuter des requêtes avancées.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a9281bc333b3edd12501a6813f12b9e7ad1bf59f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219265"
---
# <a name="advanced-resource-graph-queries"></a>Requêtes Resource Graph avancées

Pour comprendre comment fonctionnent les requêtes dans Azure Resource Graph, vous devez au préalable vous familiariser avec le [langage de requête](../concepts/query-language.md). Si vous ne connaissez pas bien [l’Explorateur de données Azure](../../../data-explorer/data-explorer-overview.md), nous vous recommandons de passer en revue les notions de base de ce langage pour apprendre à composer des requêtes pour les ressources qui vous intéressent.

Nous allons vous guider tout au long des requêtes avancées suivantes :

> [!div class="checklist"]
> - [Obtenir la taille et la capacité VMSS](#vmss-capacity)
> - [Lister tous les noms d’étiquette](#list-all-tags)
> - [Machines virtuelles mises en correspondance par expression régulière](#vm-regex)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="language-support"></a>Support multilingue

Azure CLI (par le biais d’une extension) et Azure PowerShell (par le biais d’un module) prennent en charge Azure Resource Graph. Avant d’exécuter les requêtes suivantes, vérifiez que votre environnement est prêt. Consultez [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) et [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pour savoir comment installer et valider l’environnement de votre interpréteur de commandes.

## <a name="vmss-capacity"></a>Obtenir la taille et la capacité VMSS

Cette requête recherche des ressources de groupe de machines virtuelles identiques (VMSS) et obtient divers détails, notamment la taille de machine virtuelle et la capacité du groupe identique. Ces informations utilisent la fonction `toint()` pour effectuer un cast de la capacité sur un nombre afin de pouvoir la trier. Cette opération renomme également les valeurs retournées dans les propriétés nommées personnalisées.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```powershell
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Lister tous les noms d’étiquette

Cette requête commence par l’étiquette et génère un objet JSON listant tous les noms d’étiquette uniques et leurs types correspondants.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```powershell
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Machines virtuelles mises en correspondance par expression régulière

Cette requête recherche les machines virtuelles qui correspondent à une [expression régulière](/dotnet/standard/base-types/regular-expression-language-quick-reference) (connue sous le nom _regex_).
La valeur **correspond à regex @** nous permet de définir l’expression régulière de correspondance, c’est-à-dire **^Contoso(.*)[0-9]+$**. Cette définition d’expression régulière est expliquée comme suit :

- `^` : doit correspondance au début au commencement de la chaîne.
- `Contoso` : la chaîne principale que nous mettons en correspondance (respecte la casse).
- `(.*)` : une correspondance de sous-expression :
  - `.` : correspond à n'importe quel caractère unique (sauf une nouvelle ligne).
  - `*` : correspond à l’élément précédent zéro fois ou plusieurs fois.
- `[0-9]` : correspondance de groupe de caractères pour les nombres de 0 à 9.
- `+` : correspond à l’élément précédent une fois ou plusieurs fois.
- `$` : la correspondance avec l’élément précédent doit se produire à la fin de la chaîne.

Après la mise en correspondance par nom, la requête projette le nom et effectue le tri par nom croissant.

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples de [requêtes de démarrage](starter.md)
- En savoir plus sur le [langage de requête](../concepts/query-language.md)
- Apprendre à [explorer les ressources](../concepts/explore-resources.md)