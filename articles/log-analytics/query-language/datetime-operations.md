---
title: Utilisation des valeurs de date et d’heure dans des requêtes Azure Log Analytics | Microsoft Docs
description: Décrit comment utiliser des données de date et d’heure dans des requêtes Log Analytics.
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
ms.openlocfilehash: 9b0c58fdbfb0d55b3b8998f4edfc1222b9a3d4aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988595"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Utilisation des valeurs de date et d’heure dans des requêtes Log Analytics

> [!NOTE]
> Vous devez suivre [Bien démarrer avec le portail Analytics](get-started-analytics-portal.md) et [Bien démarrer avec les requêtes](get-started-queries.md) avant d’effectuer cette leçon.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Cet article décrit comment utiliser des données de date et d’heure dans des requêtes Log Analytics.


## <a name="date-time-basics"></a>Notions de base de date et d’heure
Le langage de requête Log Analytics comporte deux principaux types de données associés aux dates et heures : datetime et timespan. Toutes les dates sont exprimées au format UTC. Même si plusieurs formats datetime sont pris en charge, le format ISO8601 est préféré. 

Les types timespan sont exprimés en tant que valeur décimale suivie d’une unité de temps :

|abréviation   | unité de temps    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | seconde       |
|ms          | milliseconde  |
|microsecond | microseconde  |
|graduation        | nanoseconde   |

Les types datetime peuvent être créés en castant une chaîne avec l’opérateur `todatetime`. Par exemple, pour passer en revue les pulsations de machine virtuelle envoyées dans un laps de temps spécifique, vous pouvez utiliser [l’opérateur between](https://docs.loganalytics.io/docs/Language-Reference/Scalar-operators/between-operator) qui est pratique pour spécifier une plage de temps.

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

Vous pouvez voir que la colonne _timeAgo_ contient des valeurs telles que « 00:09:31.5118992 », ce qui signifie qu’elles sont mises en forme comme hh:mm:ss.fffffff. Si vous souhaitez mettre en forme ces valeurs selon le _numver_ de minutes depuis l’heure de début, il suffit de diviser cette valeur par « 1 minute » :

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agrégations et création de compartiments par intervalles de temps
Un autre scénario très courant est la nécessité d’obtenir des statistiques sur une certaine période de temps dans un fragment de temps particulier. Pour ce faire, un opérateur `bin` peut être utilisé dans le cadre d’une clause de synthèse.

Utilisez la requête suivante pour obtenir le nombre d’événements qui se sont produits toutes les 5 minutes pendant la dernière demi-heure :

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Cela produit le tableau suivant :  
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

Cela génère les résultats suivants :

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7 136|
|2018-07-29T00:00:00.000|12 315|
|2018-07-30T00:00:00.000|16 847|
|2018-07-31T00:00:00.000|12 616|
|2018-08-01T00:00:00.000|5 416  |


## <a name="time-zones"></a>Fuseaux horaires
Dans la mesure où toutes les valeurs datetime sont exprimées au format UTC, il est souvent utile de les convertir dans le fuseau horaire local. Par exemple, utilisez ce calcul pour convertir les heures UTC en PST :

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Fonctions connexes

| Catégorie | Fonction |
|:---|:---|
| Convertir les types de données | [todatetime](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/todatetime())  [totimespan](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/totimespan())  |
| Arrondir la valeur à la taille de compartiment | [bin](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/bin()) |
| Obtenir une date ou heure spécifique | [ago](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago()) [now](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/now())   |
| Obtenir une partie de valeur | [datetime_part](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/datetime_part()) [getmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getmonth()) [monthofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/monthofyear()) [getyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getyear()) [dayofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofmonth()) [dayofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofweek()) [dayofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofyear()) [weekofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/weekofyear()) |
| Obtenir une date par rapport à une valeur  | [endofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofday()) [endofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofweek()) [endofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofmonth()) [endofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofyear()) [startofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofday()) [startofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofweek()) [startofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofmonth()) [startofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofyear()) |

## <a name="next-steps"></a>Étapes suivantes
Consultez d’autres leçons pour l’utilisation du langage de requête Log Analytics :

- [Opérations de chaîne](string-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Écriture de requêtes avancées](advanced-query-writing.md)
- [Jointures](joins.md)
- [Graphiques](charts.md)