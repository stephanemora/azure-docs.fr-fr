---
title: Comprendre le langage de requête
description: Décrit les tables Resource Graph et les fonctions, opérateurs et types de données Kusto disponibles, utilisables avec Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78927499"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Présentation du langage de requête Azure Resource Graph

Le langage de requête pour Azure Resource Graph prend en charge un certain nombre d’opérateurs et de fonctions. Leur fonctionnement et leur utilisation s’appuient sur le [langage de requête Kusto (KQL, Kusto Query Language)](/azure/kusto/query/index). Pour en savoir plus sur le langage de requête utilisé par Resource Graph, commencez par suivre le [tutoriel KQL](/azure/kusto/query/tutorial).

Cet article traite des composants de langage pris en charge par Resource Graph :

- [Tables Resource Graph](#resource-graph-tables)
- [Éléments du langage KQL pris en charge](#supported-kql-language-elements)
- [Caractères d’échappement](#escape-characters)

## <a name="resource-graph-tables"></a>Tables Resource Graph

Resource Graph fournit plusieurs tables contenant les données qu’il stocke, relatives aux types de ressources Resource Manager et à leurs propriétés. Vous pouvez utiliser ces tables avec l’opérateur `join` ou `union` pour récupérer des propriétés à partir des types de ressources associés. Voici la liste des tables disponibles dans Resource Graph :

|Tables Resource Graph |Description |
|---|---|
|Ressources |Table par défaut si aucune table n’est définie dans la requête. Elle contient la plupart des types de ressources et propriétés Resource Manager. |
|ResourceContainers |Inclut les données et les types de ressources d’abonnement (en préversion : `Microsoft.Resources/subscriptions`) et de groupe de ressources (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AdvisorResources |Inclut les ressources _associées_ à `Microsoft.Advisor`. |
|AlertsManagementResources |Inclut les ressources _associées_ à `Microsoft.AlertsManagement`. |
|MaintenanceResources |Inclut les ressources _associées_ à `Microsoft.Maintenance`. |
|SecurityResources |Inclut les ressources _associées_ à `Microsoft.Security`. |

Pour obtenir une liste complète des types de ressources, consultez [Référence : Tables et types de ressources pris en charge](../reference/supported-tables-resources.md).

> [!NOTE]
> La table _Resources_  est la table par défaut. Quand vous interrogez la table _Resources_ , il n’est pas nécessaire d’indiquer le nom de la table, sauf si vous utilisez `join` ou `union`. Toutefois, la pratique recommandée consiste à toujours inclure la table initiale dans la requête.

Utilisez l’Explorateur Resource Graph dans le portail pour découvrir les types de ressources disponibles dans chaque table. Vous pouvez également utiliser une requête telle que `<tableName> | distinct type` pour obtenir la liste des types de ressources existant dans votre environnement, pris en charge par la table Resource Graph concernée.

La requête suivante illustre un opérateur `join` simple. Le résultat de la requête fusionne les colonnes, et le suffixe **1** est ajouté à tous les noms de colonnes dupliqués de la table jointe (_ResourceContainers_ dans cet exemple). Comme la table _ResourceContainers_ possède des types pour les abonnements et les groupes de ressources, l’un ou l’autre de ces types peut être utilisé pour la jointure à la ressource à partir de la table _Resources_.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

La requête suivante illustre une utilisation plus complexe de l’opérateur `join`. La requête limite la table jointe aux ressources d’abonnement et utilise `project` pour inclure uniquement le champ d’origine _subscriptionId_ et le champ _name_ renommé _SubName_. Le renommage de champ évite que `join` ne l’ajoute en tant que _name1_, puisque le champ existe déjà dans _Resources_. La table d’origine est filtrée avec `where` et le `project` suivant comprend des colonnes des deux tables. Le résultat de la requête est un coffre de clés unique affichant le type, le nom du coffre de clés et le nom de l’abonnement dans lequel il se trouve.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Quand vous limitez les résultats de `join` avec `project`, la propriété utilisée par `join` pour associer les deux tables (_subscriptionId_  dans l’exemple ci-dessus) doit être incluse dans `project`.

## <a name="supported-kql-language-elements"></a>Éléments du langage KQL pris en charge

Resource Graph prend en charge tous les [types de données](/azure/kusto/query/scalar-data-types/), [fonctions scalaires](/azure/kusto/query/scalarfunctions), [opérateurs scalaires](/azure/kusto/query/binoperators) et [fonctions d’agrégation](/azure/kusto/query/any-aggfunction) du langage KQL. Des [opérateurs tabulaires](/azure/kusto/query/queries) spécifiques sont pris en charge par Resource Graph, dont certains présentent différents comportements.

### <a name="supported-tabulartop-level-operators"></a>Opérateurs tabulaires/de niveau supérieur pris en charge

Voici la liste des opérateurs tabulaires KQL pris en charge par Resource Graph avec des exemples spécifiques :

|KQL |Exemple de requête Resource Graph |Notes |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Compter les coffres de clés](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Afficher des valeurs distinctes pour un alias spécifique](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[Compter les machines virtuelles par type de système d’exploitation](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Coffre de clés avec nom d’abonnement](../samples/advanced.md#join) |Variantes de jointure prises en charge : [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite de 3 `join` dans une requête unique. Les stratégies de jointure personnalisées comme la jointure de diffusion ne sont pas autorisées. Peut être utilisé dans une table unique ou entre les tables _Resources_ et _ResourceContainers_. |
|[limit](/azure/kusto/query/limitoperator) |[Lister toutes les adresses IP publiques](../samples/starter.md#list-publicip) |Identique à `take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Opérateur hérité, utilisez `mv-expand` à la place. Valeur _RowLimit_ maximale de 400. La valeur par défaut est 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Lister Cosmos DB avec des emplacements d’écriture spécifiques](../samples/advanced.md#mvexpand-cosmosdb) |Valeur _RowLimit_ maximale de 400. La valeur par défaut est 128. |
|[order](/azure/kusto/query/orderoperator) |[Lister les ressources triées par nom](../samples/starter.md#list-resources) |Identique à `sort` |
|[project](/azure/kusto/query/projectoperator) |[Lister les ressources triées par nom](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Supprimer des colonnes des résultats](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Lister les ressources triées par nom](../samples/starter.md#list-resources) |Identique à `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Compter les ressources Azure](../samples/starter.md#count-resources) |Première page simplifiée uniquement |
|[take](/azure/kusto/query/takeoperator) |[Lister toutes les adresses IP publiques](../samples/starter.md#list-publicip) |Identique à `limit` |
|[top](/azure/kusto/query/topoperator) |[Afficher les cinq premières machines virtuelles par nom et leur type de système d’exploitation](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Combiner les résultats de deux requêtes en un résultat unique](../samples/advanced.md#unionresults) |Table unique autorisée : _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. Limite de 3 sections `union` dans une requête unique. La résolution approximative des tables avec sections `union` n’est pas autorisée. Peut être utilisé dans une table unique ou entre les tables _Resources_ et _ResourceContainers_. |
|[where](/azure/kusto/query/whereoperator) |[Afficher les ressources contenant storage](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Caractères d'échappement

Certains noms propres, tels que ceux qui incluent un `.` ou `$`, doivent être inclus dans un wrapper ou échappés dans la requête, sinon le nom de la propriété est interprété de façon incorrecte et les résultats attendus ne sont pas obtenus.

- `.`- Encapsulez le nom de la propriété de cette façon : `['propertyname.withaperiod']`
  
  Exemple de requête qui encapsule la propriété _odata.type_ :

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - Échappez le caractère dans le nom de la propriété. Le caractère d’échappement utilisé dépend de l’interpréteur de commandes Resource Graph sur lequel il est exécuté.

  - **bash** - `\`

    Exemple de requête qui échappe la propriété _\$type_ dans bash :

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - ne pas échapper le caractère `$`.

  - **PowerShell** - ``` ` ```

    Exemple de requête qui échappe la propriété _\$type_ dans PowerShell :

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Étapes suivantes

- Examinez le langage utilisé dans les [requêtes de démarrage](../samples/starter.md).
- Examinez les utilisations avancées dans les [Requêtes avancées](../samples/advanced.md).
- Découvrez plus en détails comment [explorer des ressources](explore-resources.md).