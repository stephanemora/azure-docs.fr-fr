---
title: Exemples de requêtes avancées
description: Utilisez Azure Resource Graph afin d’exécuter certaines requêtes avancées, notamment pour gérer la fonctionnalité de groupe de machines virtuelles identiques, lister toutes les étiquettes employées et faire correspondre des machines virtuelles à des expressions régulières.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 668dfc908418792c1258d112fbdfb640e85abbe2
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980309"
---
# <a name="advanced-resource-graph-queries"></a>Requêtes Resource Graph avancées

Pour comprendre comment fonctionnent les requêtes dans Azure Resource Graph, vous devez au préalable vous familiariser avec le [langage de requête](../concepts/query-language.md). Si vous ne connaissez pas bien l’[Explorateur de données Azure](../../../data-explorer/data-explorer-overview.md), nous vous recommandons de passer en revue les notions de base de ce langage pour apprendre à composer des requêtes pour les ressources qui vous intéressent.

Nous allons vous guider tout au long des requêtes avancées suivantes :

> [!div class="checklist"]
> - [Obtenir une capacité et une taille de groupe de machines virtuelles identiques](#vmss-capacity)
> - [Lister tous les noms d’étiquette](#list-all-tags)
> - [Machines virtuelles mises en correspondance par expression régulière](#vm-regex)
> - [Inclure les noms de locataire et d’abonnement avec DisplayNames](#displaynames)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="language-support"></a>Support multilingue

Azure CLI (par le biais d’une extension) et Azure PowerShell (par le biais d’un module) prennent en charge Azure Resource Graph. Avant d’exécuter les requêtes suivantes, vérifiez que votre environnement est prêt. Consultez [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) et [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pour savoir comment installer et valider l’environnement de votre interpréteur de commandes.

## <a name="vmss-capacity"></a>Obtenir une capacité et une taille de groupe de machines virtuelles identiques

Cette requête recherche des ressources de groupe de machines virtuelles identiques et obtient divers détails, notamment la taille de machine virtuelle et la capacité du groupe identique. Cette requête utilise la fonction `toint()` pour effectuer un cast de la capacité sur un nombre afin de pouvoir la trier. Enfin, les colonnes sont renommées dans les propriétés nommées personnalisées.

```kusto
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Lister tous les noms d’étiquette

Cette requête commence par l’étiquette et génère un objet JSON listant tous les noms d’étiquette uniques et leurs types correspondants.

```kusto
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Machines virtuelles mises en correspondance par expression régulière

Cette requête recherche les machines virtuelles qui correspondent à une [expression régulière](/dotnet/standard/base-types/regular-expression-language-quick-reference) (connue sous le nom _regex_). La valeur **correspond à regex \@** nous permet de définir l’expression régulière de correspondance, c’est-à-dire `^Contoso(.*)[0-9]+$`.
Cette définition d’expression régulière est expliquée comme suit :

- `^` : doit correspondance au début au commencement de la chaîne.
- `Contoso` : chaîne sensible à la casse.
- `(.*)` : correspondance de sous-expression :
  - `.` : correspond à n'importe quel caractère unique (sauf une nouvelle ligne).
  - `*` : correspond à l’élément précédent zéro fois ou plusieurs fois.
- `[0-9]` : correspondance de groupe de caractères pour les nombres de 0 à 9.
- `+` : correspond à l’élément précédent une fois ou plusieurs fois.
- `$` : la correspondance avec l’élément précédent doit se produire à la fin de la chaîne.

Après la mise en correspondance par nom, la requête projette le nom et effectue le tri par nom croissant.

```kusto
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="displaynames"></a>Inclure les noms de locataire et d’abonnement avec DisplayNames

Cette requête utilise le nouveau paramètre **Include** avec l’option _DisplayNames_ pour ajouter **subscriptionDisplayName** et **tenantDisplayName** aux résultats. Ce paramètre est disponible uniquement pour Azure CLI et Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Si la requête n’utilise pas **project** pour spécifier les propriétés retournées, **subscriptionDisplayName** et **tenantDisplayName** sont automatiquement inclus dans les résultats.
> Si la requête utilise **project**, chacun des champs _DisplayName_ doit être explicitement inclus dans **project**. Sinon, ils ne seront pas retournés dans les résultats, même si le paramètre **Include** est utilisé.

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples de [requêtes de démarrage](starter.md)
- En savoir plus sur le [langage de requête](../concepts/query-language.md)
- Apprendre à [explorer les ressources](../concepts/explore-resources.md)