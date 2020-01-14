---
title: Utilisation de valeurs de date et d'heure dans les requêtes de journal Azure Monitor | Microsoft Docs
description: Explique comment utiliser des données de date et d'heure dans les requêtes de journal Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d659be5b817317e7cec5726718f154825674349e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365340"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Utilisation de valeurs de date et d'heure dans les requêtes de journal Azure Monitor

> [!NOTE]
> Vous devez suivre [Bien démarrer avec le portail Analytics](get-started-portal.md) et [Bien démarrer avec les requêtes](get-started-queries.md) avant d’effectuer cette leçon.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Cet article explique comment utiliser des données de date et d'heure dans les requêtes de journal Azure Monitor.


## <a name="date-time-basics"></a>Notions de base de date et d’heure
Le langage de requête de Kusto comporte deux principaux types de données associés aux dates et heures : datetime et timespan. Toutes les dates sont exprimées au format UTC. Même si plusieurs formats datetime sont pris en charge, le format ISO8601 est préféré. 

Les types timespan sont exprimés en tant que valeur décimale suivie d’une unité de temps :

|abréviation   | unité de temps    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | milliseconde  |
|microseconde | microseconde  |
|graduation        | nanoseconde   |

Les types datetime peuvent être créés en castant une chaîne avec l’opérateur `todatetime`. Par exemple, pour passer en revue les pulsations de machine virtuelle envoyées dans un laps de temps spécifique, utilisez l’opérateur `between` pour spécifier une plage de temps.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Un autre scénario courant est la comparaison d’une valeur datetime au présent. Par exemple, pour afficher toutes les pulsations au cours des deux dernières minutes, vous pouvez utiliser l’opérateur `now` avec une valeur timespan qui représente deux minutes :

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Un raccourci est également disponible pour cette fonction :
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

La méthode la plus rapide et la plus lisible consiste cependant à utiliser l’opérateur `ago` :
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Supposons qu’au lieu de connaître l’heure de début et de fin, vous connaissiez l’heure de début et la durée. Vous pouvez réécrire la requête comme suit :

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Conversion des unités de temps
Il peut être utile d’exprimer une valeur datetime ou timespan dans une unité de temps autre que celle par défaut. Par exemple, supposons que vous examinez les événements d’erreur des 30 dernières minutes et avez besoin d’une colonne calculée qui affiche le temps écoulé depuis que l’événement s’est produit :

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

La colonne `timeAgo` conserve des valeurs telles que : « 00:09:31.5118992 », ce qui signifie qu’elles sont présentées selon le format hh:mm:ss.fffffff. Si vous souhaitez mettre en forme ces valeurs selon le `numver` de minutes depuis l’heure de début, divisez cette valeur par « 1 minute » :

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agrégations et création de compartiments par intervalles de temps
Un autre scénario courant est la nécessité d’obtenir des statistiques sur une certaine période de temps dans un fragment de temps particulier. Pour ce scénario, un opérateur `bin` peut être utilisé dans le cadre d’une clause de synthèse.

Utilisez la requête suivante pour obtenir le nombre d’événements qui se sont produits toutes les 5 minutes pendant la dernière demi-heure :

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Cette requête produit le tableau suivant :  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Une autre façon de créer des compartiments de résultats consiste à utiliser des fonctions, telles que `startofday` :

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Cette requête produit les résultats suivants :

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7 136|
|2018-07-29T00:00:00.000|12 315|
|2018-07-30T00:00:00.000|16 847|
|2018-07-31T00:00:00.000|12 616|
|2018-08-01T00:00:00.000|5 416|


## <a name="time-zones"></a>Fuseaux horaires
Dans la mesure où toutes les valeurs datetime sont exprimées au format UTC, il est souvent utile de convertir ces valeurs dans le fuseau horaire local. Par exemple, utilisez ce calcul pour convertir les heures UTC en PST :

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Fonctions connexes

| Category | Fonction |
|:---|:---|
| Convertir les types de données | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Arrondir la valeur à la taille de compartiment | [bin](/azure/kusto/query/binfunction) |
| Obtenir une date ou heure spécifique | [ago](/azure/kusto/query/agofunction) [now](/azure/kusto/query/nowfunction)   |
| Obtenir une partie de valeur | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Obtenir une valeur de date relative  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous à d’autres leçons sur l’utilisation du [langage de requête Kusto](/azure/kusto/query/) avec des données de journal Azure Monitor :

- [Opérations de chaîne](string-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Écriture de requêtes avancées](advanced-query-writing.md)
- [Joins](joins.md)
- [Graphiques](charts.md)
