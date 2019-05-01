---
title: Détection des anomalies de série chronologique et prévision dans l’Explorateur de données Azure
description: Découvrez comment analyser les données chronologiques pour la détection des anomalies et les prévisions à l’aide de l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 934dfd3334b6f433c7acdf9816a3fb5e24f0430f
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872004"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Détection d’anomalies et les prévisions dans l’Explorateur de données Azure

Explorateur de données Azure effectue la collection en cours de données de télémétrie à partir des services cloud ou d’appareils IoT. Ces données sont analysées pour différents insights comme la surveillance de l’intégrité du service, processus de production physiques, les tendances d’utilisation et prévision de charge. L’analyse est effectuée sur les séries chronologiques des mesures sélectionnées pour localiser un modèle de l’écart de la mesure par rapport à son modèle de ligne de base normale standard. Explorateur de données Azure contient la prise en charge native pour la création, la manipulation et l’analyse de plusieurs séries chronologiques. Il peut créer et analyser des milliers de série chronologique en quelques secondes, l’activation de quasiment en temps réel surveillance des solutions et des flux de travail.

Cet article décrit en détail l’Explorateur de données Azure temps série de détection et de prévision fonctionnalités des anomalies. Les fonctions de série de temps applicable sont basées sur un modèle de décomposition connu robuste, où chaque série chronologique d’origine est décomposé en tendances saisonnières, et certains composants subsistant. Anomalies sont détectées par les valeurs hors norme sur le composant résiduel, tandis que la prévision s’effectue en extrapolant les saisonniers et composants de tendance. L’implémentation de l’Explorateur de données Azure améliore considérablement le modèle de décomposition de base par la détection de saisonnalité automatique, l’analyse des valeurs hors norme robuste et implémentation vectorisée manuscrite pour traiter des milliers de série chronologique en quelques secondes.

## <a name="prerequisites"></a>Conditions préalables

Lecture [temps d’analyse de série dans l’Explorateur de données Azure](/azure/data-explorer/time-series-analysis) pour une vue d’ensemble des fonctionnalités de série de temps.

## <a name="time-series-decomposition-model"></a>Modèle de décomposition de série chronologique

Une implémentation native Explorateur de données Azure pour la prédiction de série chronologique et de détection d’anomalie utilise un modèle de décomposition bien connu. Ce modèle est appliqué à la série chronologique de mesures prévus au manifeste périodiques et comportement de tendance, telles que le trafic de service, les pulsations de composant et IoT des mesures périodiques pour prévoir les futures valeurs métriques et détecter celles anormales. L’hypothèse de ce processus de régression qui est autre que précédemment connu saisonniers et le comportement de tendance, le temps série distribuée de manière aléatoire. Vous pouvez ensuite prévoir les futures valeurs des métriques à partir de la saison et composants de tendance, collectivement nommés de la ligne de base et ignorer la partie résiduelle. Vous pouvez également détecter des valeurs anormales basés sur l’analyse des valeurs hors norme en utilisant uniquement la partie qui sont restée.
Pour créer un modèle de décomposition, utilisez la fonction [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). Le `series_decompose()` fonction prend un ensemble de séries chronologiques et automatiquement décompose chaque série chronologique pour son saisonniers, tendance, résiduel et les composants de base. 

Par exemple, vous pouvez décomposer le trafic d’un service web interne à l’aide de la requête suivante :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Décomposition de série temporelle](media/anomaly-detection/series-decompose-timechart.png)

* La série chronologique d’origine est étiquetée **num** (en rouge). 
* Le processus commence par la détection automatique de la saisonnalité en utilisant la fonction [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) et extrait le **saisonniers** modèle (en violet).
* Le modèle saisonnier est soustraite de la série chronologique d’origine et une régression linéaire est exécutée à l’aide de la fonction [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) pour trouver la **tendance** composant (en bleu clair).
* La fonction soustrait la tendance et le reste est le **résiduel** composant (en vert).
* Enfin, la fonction ajoute les saisonniers et des tendances de composants afin de générer le **baseline** (en bleu).

## <a name="time-series-anomaly-detection"></a>Détection des anomalies de série chronologique

La fonction [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) recherche des points anormales sur un ensemble de séries chronologiques. Cette fonction appelle `series_decompose()` pour générer le modèle de décomposition, puis exécute [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) sur le composant résiduel. `series_outliers()` calcule les scores d’anomalie pour chaque point du composant qui sont resté à l’aide du test de Tukey frontière de sécurité. Les scores d’anomalie au-dessus 1.5 ou en dessous -1,5 indiquent une augmentation des anomalies douce ou refuser respectivement. Les scores d’anomalie au-dessus 3.0 ou en dessous -3,0 indiquent une anomalie forte. 

La requête suivante permet de détecter les anomalies dans le trafic du service web interne :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Détection des anomalies de série chronologique](media/anomaly-detection/series-anomaly-detection.png)

* La série chronologique d’origine (en rouge). 
* La ligne de base (saisonniers + tendance) composant (en bleu).
* Les points anormales (en violet) en haut de la série chronologique d’origine. Les points anormales écartent sensiblement les valeurs de base attendu.

## <a name="time-series-forecasting"></a>Prévision de séries chronologiques

La fonction [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) prédit des valeurs futures d’un ensemble de séries chronologiques. Cette fonction appelle `series_decompose()` pour générer la décomposition de modèle, puis, pour chaque série chronologique, extrapole le composant de ligne de base dans le futur.

La requête suivante permet de prédire le trafic du service web de la semaine suivante :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Prévision de séries chronologiques](media/anomaly-detection/series-forecasting.png)

* Métrique d’origine (en rouge). Les valeurs futures sont manquants et 0, la valeur par défaut.
* Extrapolez le composant de ligne de base (en bleu) pour prédire des valeurs de la semaine prochaine.

## <a name="scalability"></a>Extensibilité

Syntaxe de langage de requête Explorateur de données Azure permet un seul appel à traiter plusieurs séries chronologiques. Son implémentation optimisée unique permet pour accélérer les performances, ce qui est essentiel pour la détection d’anomalie efficace et lors de l’analyse des milliers de compteurs dans des scénarios en temps réel quasi de prévision.

La requête suivante montre le traitement des trois séries chronologiques simultanément :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Évolutivité de série heure](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Résumé

Ce document décrit en détail des fonctions natives de l’Explorateur de données Azure pour la détection des anomalies de série chronologique et prévision, ce qui améliore considérablement le modèle de décomposition de base sur lequel il est basé. Chaque série chronologique d’origine est décomposé, anomalies détectées, et les prévisions effectuées. Temps série d’anomalie de détection et de la prévision de fonctionnalités servent à près de scénarios d’analyse en temps réel, telles que la détection des erreurs, la maintenance prédictive et à la demande et de prévision de charge.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [d’apprentissage fonctionnalités](/azure/data-explorer/machine-learning-clustering) dans l’Explorateur de données Azure.