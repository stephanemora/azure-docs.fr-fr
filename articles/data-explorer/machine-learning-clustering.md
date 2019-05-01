---
title: Apprentissage de fonctionnalité dans l’Explorateur de données Azure
description: Utiliser l’apprentissage de clustering pour l’analyse de Cause racine dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 2358cb2ea411a0077f34798183da30bd32ae067b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925122"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Apprentissage de fonctionnalité dans l’Explorateur de données Azure

Explorateur de données Azure, une plateforme d’analytique de Big Data, est utilisé pour surveiller l’intégrité du service, qualité de service ou des périphériques ne fonctionnant pas correctement pour un comportement anormal à l’aide intégrée [de détection des anomalies et les prévisions](/azure/data-explorer/anomaly-detection) fonctions. Une fois qu’un modèle anormal est détecté, une analyse (la cause racine) est effectuée pour atténuer ou de résoudre l’anomalie.

Le processus de diagnostic est complexe et longue et effectués par des experts du domaine. Le processus comprend l’extraction et de joindre des données supplémentaires à partir de sources différentes pour le même laps de temps, recherche de modifications dans la distribution des valeurs sur plusieurs dimensions, des variables supplémentaires, de création de graphiques et d’autres techniques en fonction des connaissances de domaine et intuition. Dans la mesure où ces scénarios de diagnostic sont courants dans l’Explorateur de données Azure, le plug-ins de machine learning sont disponibles pour faciliter la phase de diagnostic et de raccourcir la durée de la RCA.

Explorateur de données Azure a trois plug-ins de Machine Learning : [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), et [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Tous les plug-ins implémentent des algorithmes de clustering. Le `autocluster` et `basket` plug-ins de cluster un jeu d’enregistrements unique et la `diffpatterns` plug-in de clusters les différences entre deux jeux d’enregistrements.

## <a name="clustering-a-single-record-set"></a>Clustering d’un jeu d’enregistrements unique

Un scénario courant inclut un jeu de données sélectionné en fonction de critères spécifiques tels que de la fenêtre de temps présentant un comportement anormal, relevés d’appareil de température maximale, les commandes de longue durée et dépenses des utilisateurs. Nous aimerions un moyen simple et rapide pour rechercher des modèles courants (segments) dans les données. Les modèles sont un sous-ensemble du jeu de données dont les enregistrements partagent les mêmes valeurs sur plusieurs dimensions (colonnes catégorielles). La requête suivante génère et affiche une série chronologique d’exceptions de service sur une semaine dans des emplacements de dix minutes :

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Graphique temporel les exceptions de service](media/machine-learning-clustering/service-exceptions-timechart.png)

Le nombre d’exceptions service met en corrélation avec l’ensemble du trafic de service. Vous pouvez voir clairement le modèle quotidiens, pour les jours du lundi au vendredi, avec un augmentation du service exception compte intermédiaire jour, supprime de nombres pendant la nuit. Nombre faible est visibles pendant le week-end. Pics d’exception peuvent être détectés à l’aide de [temps de détection des anomalies de série](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) dans l’Explorateur de données Azure.

Le deuxième pic dans les données se produit sur mardi après-midi. La requête suivante est utilisée pour diagnostiquer ce pic. Utilisez la requête pour redessiner le graphique autour du pic de résolution plus élevée (huit heures dans des emplacements d’une minute) pour vérifier s’il s’agit d’un pic sharp et afficher ses bordures.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Se concentrer sur le graphique temporel pic](media/machine-learning-clustering/focus-spike-timechart.png)

Nous voyons un pic de deux minutes étroit de 15:00 à 15 h 02. Dans la requête suivante, compter les exceptions dans cette fenêtre de deux minutes :

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Nombre |
|---------|
|972    |

Dans la requête suivante, exemple 20 exceptions hors 972 :

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Région | ScaleUnit | DeploymentId                     | Point de trace | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Utilisez `autocluster()` pour un enregistrement unique, définissez le clustering

Même s’il existe des exceptions de moins d’un millier, il est toujours difficile à repérer les segments courantes, comme il existe plusieurs valeurs dans chaque colonne. Vous pouvez utiliser [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) plug-in pour extraire une petite liste de segments communs et trouver l’intéressant instantanément des clusters dans les deux minutes du Pique-notes comme indiqué dans la requête suivante :

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| ID de segment | Nombre | Pourcentage | Région | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | à jour su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Vous pouvez voir dans les résultats ci-dessus que le segment dominant contient % 65.74 des enregistrements exception totale et qu’il partage quatre dimensions. Le segment suivant est beaucoup moins courant, contient seulement 9.67 % des enregistrements et partage des trois dimensions. Les autres segments sont encore moins courantes. 

Autocluster utilise un algorithme propriétaire pour plusieurs dimensions d’exploration de données et l’extraction des segments intéressantes. « Intéressantes » signifie que chaque segment a un avantage important de l’ensemble d’enregistrements et le jeu de fonctionnalités. Les segments sont également divergent, ce qui signifie que chacun d’eux est considérablement différent des autres. Un ou plusieurs de ces segments peuvent être pertinentes pour le processus RCA. Pour réduire la révision de segment et d’évaluation, autocluster extrait uniquement une liste de petit segment.

### <a name="use-basket-for-single-record-set-clustering"></a>Utilisez `basket()` pour un enregistrement unique, définissez le clustering

Vous pouvez également utiliser le [ `basket()` ](/azure/kusto/query/basketplugin) plug-in comme indiqué dans la requête suivante :

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| ID de segment | Nombre | Pourcentage | Région | ScaleUnit | DeploymentId | Point de trace | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5. | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6. | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | à jour su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Panier d’achat implémente l’algorithme Apriori pour l’élément d’exploration de données et extrait tous les segments dont la couverture du jeu d’enregistrements est au-dessus d’un seuil (valeur par défaut 5 %). Vous pouvez voir que les autres segments ont été extraits avec les visages similaires (par exemple, les segments 0,1 ou 2, 3).

Les plug-ins sont puissantes et faciles à utiliser, mais leur limite significative est dû au fait que leur cluster un unique jeu d’enregistrements dans une manière non supervisée (avec aucune étiquette). Il est donc difficile de savoir si les modèles extraits caractérisent le jeu d’enregistrements sélectionné (les enregistrements anormales) ou le jeu d’enregistrements global.

## <a name="clustering-the-difference-between-two-records-sets"></a>La différence entre les jeux de deux enregistrements de clustering

Le [ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) plug-in permet de résoudre la limitation de `autocluster` et `basket`. `Diffpatterns` prend deux jeux d’enregistrements et extrait les segments principales qui diffèrent entre eux. Un seul ensemble contient généralement le jeu en cours d’étude d’enregistrements anormale (un analysées par `autocluster` et `basket`). L’autre jeu contient le jeu d’enregistrements référence (de base). 

Dans la requête ci-dessous, nous utilisons `diffpatterns` à trouver des clusters intéressantes dans deux minutes du pic, qui diffèrent de celles des clusters au sein de la ligne de base. Nous définissons la fenêtre de ligne de base en tant que les huit minutes avant le 15:00 (lorsque le pic démarré). Nous devons également étendre par une colonne binaire (AB) spécifiant si un enregistrement spécifique appartient à la ligne de base ou à l’ensemble anormal. `Diffpatterns` implémente un algorithme d’apprentissage supervisé, où les étiquettes de deux classe ont été générées par l’anormales par rapport à l’indicateur de ligne de base (AB).

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| ID de segment | CountA | CountB | PercentA | PercentB | PercentDiffAB | Région | ScaleUnit | DeploymentId | Point de trace |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5. | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | à jour su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6. | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Le segment plus dominant est le même segment a été extrait par `autocluster`, sa couverture sur la fenêtre anormale de deux minutes est également 65.74 %. Mais sa couverture sur la fenêtre de ligne de base de huit minutes est uniquement 1.7 %. La différence est 64.04 %. Cette différence semble être liée au pic d’activité anormale. Vous pouvez vérifier cette hypothèse en fractionnant le graphique d’origine dans les enregistrements appartenant à ce segment qui pose problème et les autres segments comme indiqué dans la requête ci-dessous :

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Graphique temporel de segment 'diffpattern' lors de la validation](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Ce graphique permet de voir que le pic mardi après-midi a en raison d’exceptions à partir de ce segment spécifique, découverts à l’aide de la `diffpatterns` plug-in.

## <a name="summary"></a>Résumé

Les plug-ins de l’Explorateur de données Azure Machine Learning sont utiles dans de nombreux scénarios. Le `autocluster` et `basket` implémenter l’algorithme d’apprentissage non supervisé et sont faciles à utiliser. `Diffpatterns` implémente supervisé algorithme d’apprentissage automatique et bien plus complexe, il est plus puissant dans l’extraction des segments de différenciation pour RCA.

Ces plug-ins sont utilisés de manière interactive dans des scénarios ad-hoc et automatiques près des services de surveillance en temps réel. Dans l’Explorateur de données Azure, la détection des anomalies de série chronologique est suivie par un processus de diagnostic qui est optimisé pour répondre aux normes de niveau de performance nécessaire.
