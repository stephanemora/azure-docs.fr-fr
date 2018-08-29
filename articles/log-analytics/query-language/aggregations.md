---
title: Agrégations dans des requêtes Azure Log Analytics | Microsoft Docs
description: Décrit les fonctions d’agrégation dans des requêtes Log Analytics qui proposent des méthodes utiles pour analyser vos données.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 562fdc82e0b814fc759bda7b853492b47d073925
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190798"
---
# <a name="aggregations-in-log-analytics-queries"></a>Agrégations dans des requêtes Log Analytics

> [!NOTE]
> Vous devez suivre [Bien démarrer avec le portail Analytics](get-started-analytics-portal.md) et [Bien démarrer avec les requêtes](get-started-queries.md) avant d’effectuer cette leçon.

Cet article décrit les fonctions d’agrégation dans des requêtes Log Analytics qui proposent des méthodes utiles pour analyser vos données. Ces fonctions opèrent toutes avec l’opérateur `summarize` qui génère une table avec les résultats agrégés de la table d’entrée.

## <a name="counts"></a>Nombres

### <a name="count"></a>count
Comptez le nombre de lignes du jeu de résultats une fois que tous les filtres sont appliqués. L’exemple suivant retourne le nombre total de lignes dans la table _Perf_ au cours des 30 dernières minutes. Le résultat est retourné dans une colonne nommée *count_*, sauf si vous lui attribuez un nom spécifique :


```OQL
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```OQL
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Une visualisation de graphique temporel peut être utile pour voir une tendance au fil du temps :

```OQL
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

La sortie de cet exemple affiche la courbe de tendance du nombre d’enregistrements dans la table Perf dans des intervalles de 5 minutes :

![Tendance du nombre](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Utilisez `dcount` et `dcountif` pour compter des valeurs distinctes dans une colonne spécifique. La requête suivante évalue le nombre d’ordinateurs distincts qui ont envoyé des pulsations dans la dernière heure :

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Pour compter uniquement les ordinateurs Linux qui ont envoyé des pulsations, utilisez `dcountif` :

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Évaluation des sous-groupes
Pour effectuer un compte ou d’autres agrégations de sous-groupes dans vos données, utilisez le mot clé `by`. Par exemple, pour compter le nombre d’ordinateurs Linux distincts qui ont envoyé des pulsations dans chaque pays :

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | ordinateurs_distincts  |
------------------|---------------------|
|États-Unis    | 19                  |
|Canada           | 3                   |
|Irlande          | 0                   |
|Royaume-Uni   | 0                   |
|Pays-bas      | 2                   |


Pour analyser des sous-groupes encore plus petits de vos données, ajoutez des noms de colonnes supplémentaires à la section `by`. Par exemple, vous pouvez peut-être compter le nombre d’ordinateurs distincts de chaque pays par OSType :

```OQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Centiles et variance
Lors de l’évaluation de valeurs numériques, il est courant d’en calculer la moyenne à l’aide de `summarize avg(expression)`. Les moyennes sont affectées par les valeurs extrêmes qui ne caractérisent que quelques cas. Pour résoudre ce problème, vous pouvez utiliser des fonctions moins sensibles telles que `median` ou `variance`.

### <a name="percentile"></a>Percentile
Pour rechercher la valeur médiane, utilisez la fonction `percentile` avec une valeur pour spécifier le centile :

```OQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Vous pouvez également spécifier différents centiles afin d’obtenir un résultat agrégé pour chacun :

```OQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Cela peut indiquer que quelques UC d’ordinateur ont des valeurs médianes similaires mais, si certains sont stables autour de la valeur médiane, d’autres ordinateurs ont signalé des valeurs d’UC beaucoup plus faibles et beaucoup plus élevées, ce qui signifie la présence de pics.

### <a name="variance"></a>Variance
Pour évaluer directement la variance d’une valeur, utilisez les méthodes de variance et d’écart type :

```OQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Un bon moyen d’analyser la stabilité de l’utilisation de l’UC consiste à combiner stdev avec le calcul de la valeur médiane :

```OQL
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Consultez d’autres leçons pour l’utilisation du langage de requête Log Analytics :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Écriture de requêtes avancées](advanced-query-writing.md)
- [Jointures](joins.md)
- [Graphiques](charts.md)
