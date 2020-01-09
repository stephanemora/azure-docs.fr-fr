---
title: Écriture de requêtes de journal efficaces dans Azure Monitor | Microsoft Docs
description: Références à des ressources permettant d’apprendre à écrire des requêtes dans Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: 9281abaf7afd97573211ff20f15fa97b19724218
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365289"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Écriture de requêtes de journal efficaces dans Azure Monitor
Cet article fournit des recommandations sur l’écriture de requêtes de journal efficaces dans Azure Monitor. En adoptant ces stratégies, vous pouvez avoir la certitude que vos requêtes s’exécutent rapidement et sans surcharge importante.

## <a name="scope-your-query"></a>Étendue de votre requête
Demander à votre requête de traiter plus de données que ce dont vous avez réellement besoin peut allonger la durée d’exécution de votre requête et générer trop de données dans vos résultats pour une analyse efficace. Dans des cas extrêmes, la requête risque même d’expirer et d’échouer.

### <a name="specify-your-data-source"></a>Spécifier votre source de données
La première étape pour écrire une requête efficace consiste à en limiter l’étendue à ses sources de données incontournables. La spécification d’une table est toujours préférable à l’exécution d’une recherche dans un vaste texte, comme `search *`. Pour interroger une table spécifique, démarrez votre requête avec le nom de la table comme suit :

``` Kusto
requests | ...
```

Vous pouvez utiliser [search](/azure/kusto/query/searchoperator) pour rechercher une valeur dans plusieurs colonnes de tables spécifiques à l’aide d’une requête comme la suivante :

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Utilisez [union](/azure/kusto/query/unionoperator) pour interroger plusieurs tables comme ceci :

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Spécifier un intervalle de temps
Vous devez également limiter votre requête à l’intervalle de temps des données dont vous avez besoin. Par défaut, votre requête contient les données collectées au cours des dernières 24 heures. Vous pouvez changer cette option dans le [sélecteur d’intervalle de temps](get-started-portal.md#select-a-time-range) ou l’ajouter explicitement à votre requête. Il est préférable d’ajouter le filtre de temps immédiatement après le nom de la table afin que le reste de votre requête ne traite que les données comprises dans le bon intervalle :

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Obtenir uniquement les enregistrements les plus récents

Pour retourner uniquement les enregistrements les plus récents, utilisez l’opérateur *top* comme dans la requête suivante qui retourne les 10 derniers enregistrements journalisés dans la table *traces* :

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrer les enregistrements
Pour passer en revue uniquement les journaux d’activité qui correspondent à une condition donnée, utilisez l’opérateur *where* comme dans la requête suivante qui retourne uniquement les enregistrements dans lesquels la valeur _severityLevel_ est supérieure à 0 :

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Comparaisons de chaînes
Quand vous [évaluez des chaînes](/azure/kusto/query/datatypes-string-operators), vous devez généralement utiliser `has` au lieu de `contains` quand vous recherchez des jetons complets. L’opérateur `has` est plus efficace, car il n’a pas besoin de rechercher des sous-chaînes.

## <a name="returned-columns"></a>Colonnes retournées

Utilisez [project](/azure/kusto/query/projectoperator) pour restreindre l’ensemble de colonnes à traiter à celles nécessaires :

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Même si vous pouvez utiliser l’opérateur [extend](/azure/kusto/query/extendoperator) pour calculer des valeurs et créer vos propres colonnes, il est généralement plus efficace de filtrer sur une colonne de table.

Par exemple, la première requête ci-dessous, qui filtre sur _operation\_Name_, sera plus efficace que la seconde, qui crée une colonne _subscription_ et filtre dessus :

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Utilisation de jointures
Quand vous utilisez l’opérateur [join](/azure/kusto/query/joinoperator), choisissez de placer la table contenant le moins de lignes du côté gauche de la requête.


## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les bonnes pratiques relatives aux requêtes, consultez [Bonnes pratiques relatives aux requêtes](/azure/kusto/query/best-practices).
