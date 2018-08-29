---
title: Création de graphiques et de diagrammes à partir de requêtes Azure Log Analytics | Microsoft Docs
description: Décrit plusieurs visualisations dans Azure Log Analytics pour afficher vos données de différentes façons.
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
ms.openlocfilehash: 359e5e671287c4d330deeb2d3573877d9ee5d1c5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190793"
---
# <a name="creating-charts-and-diagrams-from-log-analytics-queries"></a>Création de graphiques et de diagrammes à partir de requêtes Log Analytics

> [!NOTE]
> Vous devez suivre [Agrégations avancées dans des requêtes Log Analytics](advanced-aggregations.md) avant d’effectuer cette leçon.

Cet article décrit plusieurs visualisations dans Azure Log Analytics pour afficher vos données de différentes façons.

## <a name="charting-the-results"></a>Affichage des résultats dans un graphique
Commencez par passer en revue le nombre d’ordinateurs par système d’exploitation au cours de la dernière heure :

```OQL
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Par défaut, les résultats sont affichés sous forme de table :

![Table](media/charts/table-display.png)

Pour obtenir un meilleur affichage, sélectionnez **Graphique**, puis choisissez l’option **Secteurs** pour visualiser les résultats :

![Graphique à secteurs](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Graphiques temporels
Affichez la moyenne, le 50e et le 95e centiles de temps processeur dans des intervalles de 1 heure. La requête génère plusieurs séries et vous pouvez ensuite sélectionner les séries à afficher dans le graphique de temps :

```OQL
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Sélectionnez l’option d’affichage de graphique **Courbes** :

![Graphique en courbes](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Ligne de référence

Une ligne de référence peut vous aider à identifier facilement si la métrique a dépassé un seuil spécifique. Pour ajouter une ligne à un graphique, étendez le jeu de données avec une colonne constante :

```OQL
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Ligne de référence](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Plusieurs dimensions
Plusieurs expressions de la clause `by` de `summarize` créent plusieurs lignes dans les résultats, une pour chaque combinaison de valeurs.

```OQL
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Quand vous affichez les résultats sous forme de graphique, il utilise la première colonne de la clause `by`. L’exemple suivant montre un histogramme empilé avec _l’EventID_. Comme les dimensions doivent être de type `string`, dans cet exemple _l’EventID_ est casté en chaîne. 

![EventID de graphique à barres](media/charts/charts-and-diagrams-multiDimension1.png)

Vous pouvez changer de valeur en sélectionnant la liste déroulante avec le nom de colonne. 

![AccountType de graphique à barres](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez d’autres leçons pour l’utilisation du langage de requête Log Analytics :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Écriture de requêtes avancées](advanced-query-writing.md)
- [Jointures](joins.md)