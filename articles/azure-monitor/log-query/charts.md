---
title: Création de graphiques et de diagrammes à partir de requêtes de journal Azure Monitor | Microsoft Docs
description: Décrit plusieurs visualisations dans Azure Monitor pour afficher vos données de journal de différentes façons.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670319"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Création de graphiques et de diagrammes à partir de requêtes de journal Azure Monitor

> [!NOTE]
> Vous devez terminer [Agrégations avancées dans des requêtes de journal Azure Monitor](advanced-aggregations.md) avant de suivre cette leçon.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Cet article décrit plusieurs visualisations dans Azure Monitor pour afficher vos données de journal de différentes façons.

## <a name="charting-the-results"></a>Affichage des résultats dans un graphique
Commencez par passer en revue le nombre d’ordinateurs par système d’exploitation au cours de la dernière heure :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Par défaut, les résultats sont affichés sous forme de table :

![Table de charge de travail](media/charts/table-display.png)

Pour obtenir un meilleur affichage, sélectionnez **Graphique**, puis choisissez l’option **Secteurs** pour visualiser les résultats :

![Graphique à secteurs](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Graphiques temporels
Affichez la moyenne, le 50e et le 95e centiles de temps processeur dans des intervalles de 1 heure. La requête génère plusieurs séries et vous pouvez ensuite sélectionner les séries à afficher dans le graphique de temps :

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Sélectionnez l’option d’affichage de graphique **Courbes** :

![Graphique en courbes](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Ligne de référence

Une ligne de référence peut vous aider à identifier facilement si la métrique a dépassé un seuil spécifique. Pour ajouter une ligne à un graphique, étendez le jeu de données avec une colonne constante :

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Ligne de référence](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Plusieurs dimensions
Plusieurs expressions de la clause `by` de `summarize` créent plusieurs lignes dans les résultats, une pour chaque combinaison de valeurs.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Quand vous affichez les résultats sous forme de graphique, il utilise la première colonne de la clause `by`. L’exemple suivant montre un histogramme empilé avec _l’EventID_. Comme les dimensions doivent être de type `string`, dans cet exemple _l’EventID_ est casté en chaîne. 

![EventID de graphique à barres](media/charts/charts-and-diagrams-multiDimension1.png)

Vous pouvez changer de valeur en sélectionnant la liste déroulante avec le nom de colonne. 

![AccountType de graphique à barres](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous à d’autres leçons sur l’utilisation du [langage de requête Kusto](/azure/kusto/query/) avec des données de journal Azure Monitor :

- [Opérations de chaîne](string-operations.md)
- [Opérations de date et d’heure](datetime-operations.md)
- [Fonctions d’agrégation](aggregations.md)
- [Agrégations avancées](advanced-aggregations.md)
- [JSON et structures de données](json-data-structures.md)
- [Écriture de requêtes avancées](advanced-query-writing.md)
- [Joins](joins.md)
