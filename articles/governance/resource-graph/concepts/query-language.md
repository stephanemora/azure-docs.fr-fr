---
title: Comprendre le langage de requête
description: Décrit les opérateurs et les fonctions Kusto disponibles et utilisables avec Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231511"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Présentation du langage de requête Azure Resource Graph

Le langage de requête pour Azure Resource Graph prend en charge un certain nombre d’opérateurs et de fonctions. Chacun fonctionne selon l’[explorateur de données Azure](../../../data-explorer/data-explorer-overview.md).

La meilleure façon d’en savoir plus sur le langage de requête utilisé par Resource Graph consiste à consulter la documentation sur le [langage de requête](/azure/kusto/query/index) de l’explorateur de données Azure. Elle fournit des informations sur la structure du langage et sur la façon de combiner les différents opérateurs et fonctions pris en charge.

## <a name="supported-tabular-operators"></a>Opérateurs tabulaires pris en charge

Voici la liste des opérateurs tabulaires pris en charge dans Resource Graph :

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Fonctions prises en charge

Voici la liste des fonctions prises en charge dans Resource Graph :

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

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

- Voir l’utilisation du langage dans les [requêtes de démarrage](../samples/starter.md)
- Voir les utilisations avancées dans les [Requêtes avancées](../samples/advanced.md)
- Apprendre à [explorer les ressources](explore-resources.md)