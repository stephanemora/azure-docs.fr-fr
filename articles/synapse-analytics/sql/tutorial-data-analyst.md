---
title: 'Tutoriel : Utiliser SQL à la demande (préversion) pour analyser des jeux de données Azure Open Datasets dans Azure Synapse Studio (préversion)'
description: Ce tutoriel vous montre comment effectuer facilement des analyses de données exploratoires en combinant différents jeux de données Azure Open Datasets avec SQL à la demande (préversion) et comment visualiser les résultats dans Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0471bd594dcf1b0654d6b4c496e5d39ade2739fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539518"
---
# <a name="tutorial-use-sql-on-demand-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Tutoriel : Utiliser SQL à la demande pour analyser des jeux de données Azure Open Datasets et visualiser les résultats dans Azure Synapse Studio

Dans ce tutoriel, vous apprenez à effectuer des analyses de données exploratoires en combinant différents jeux de données Azure Open Datasets avec SQL à la demande, puis en visualisant les résultats dans Azure Synapse Studio.

En particulier, vous analysez le [jeu de données sur les taxis de la ville de New York (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) qui comprend les éléments suivants :

- Dates et heures de début et de fin des trajets.
- Adresses de début et de fin des trajets. 
- Distance des trajets.
- Tarifs détaillés.
- Types de tarifs.
- Types de paiement. 
- Nombre de passagers indiqué par le chauffeur.

## <a name="automatic-schema-inference"></a>Inférence de schéma automatique

Les données étant stockées au format de fichier Parquet, l’inférence de schéma automatique est disponible. Il est donc possible d’interroger facilement les données sans lister les types de données de toutes les colonnes dans les fichiers. Vous pouvez aussi utiliser le mécanisme de colonne virtuelle et la fonction filepath pour filtrer un certain sous-ensemble de fichiers.

Commençons par nous familiariser avec les données sur les taxis de la ville de New York en exécutant la requête suivante :

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

L’extrait de code suivant présente le résultat pour les données sur les taxis de la ville de New York :

![Extrait du résultat des données sur les taxis de New York](./media/tutorial-data-analyst/1.png)

De même, vous pouvez interroger le jeu de données sur les jours fériés à l’aide de la requête suivante :

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

L’extrait de code suivant présente le résultat pour le jeu de données sur les jours fériés :

![Extrait du résultat du jeu de données sur les jours fériés](./media/tutorial-data-analyst/2.png)

Enfin, vous pouvez également interroger le jeu de données sur les données météorologiques à l’aide de la requête suivante :

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

L’extrait de code suivant présente le résultat pour le jeu de données sur les données météorologiques :

![Extrait du résultat du jeu de données sur les données météorologiques](./media/tutorial-data-analyst/3.png)

Pour en savoir plus sur la signification des différentes colonnes, consultez les descriptions des jeux de données [Taxis de la ville de New York](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Jours fériés](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) et [Données météorologiques](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analyse des séries chronologiques, de la saisonnalité et des valeurs hors norme

Vous pouvez facilement totaliser le nombre annuel de courses de taxi à l’aide de la requête suivante :

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

L’extrait de code suivant présente le résultat pour le nombre annuel de courses de taxi :

![Extrait du résultat du nombre annuel de courses de taxi](./media/tutorial-data-analyst/4.png)

Vous pouvez visualiser les données dans Synapse Studio en passant de la vue **Table** à la vue **Graphique**. Vous pouvez choisir parmi différents types de graphiques, par exemple **Aires**, **Barres**, **Histogramme**, **Courbes**, **Secteurs** et **Nuages de points**. Dans le cas présent, tracez l’**histogramme** avec la colonne **Category** définie sur **current_year** :

![Histogramme présentant les courses effectuées par an](./media/tutorial-data-analyst/5.png)

Cette visualisation laisse clairement apparaître une tendance à la baisse du nombre de courses sur plusieurs années. Cette baisse est vraisemblablement due à une augmentation récente du nombre d’entreprises de covoiturage.

> [!NOTE]
> Au moment de la rédaction de ce tutoriel, les données pour 2019 sont incomplètes. En conséquence, il y a une forte baisse du nombre de courses pour cette année.

À présent, concentrons l’analyse sur une seule année, par exemple, 2016. La requête suivante retourne le nombre quotidien de courses au cours de cette année :

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

L’extrait de code suivant présente le résultat de cette requête :

![Extrait du résultat du nombre quotidien de courses pour 2016](./media/tutorial-data-analyst/6.png)

Là encore, vous pouvez facilement visualiser les données en traçant l’**histogramme** avec la colonne **Category** définie sur **current_day** et la colonne **Legend (series)** définie sur **rides_per_day**.

![Histogramme présentant le nombre quotidien de courses pour 2016](./media/tutorial-data-analyst/7.png)

Dans le graphique en nuage de points, vous pouvez voir qu’il existe un modèle hebdomadaire, avec les samedis comme jour de pointe. Pendant les mois d’été, il y a moins de courses de taxi en raison des vacances. Notez également des baisses importantes du nombre de courses de taxi, sans schéma clair de quand et pourquoi elles se produisent.

À présent, voyons si les chutes sont corrélées avec les jours fériés en joignant le jeu de données sur les courses de taxi de la ville de New York au jeu de données sur les jours fériés :

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![Visualisation des résultats des jeux de données sur les courses de taxi de la ville de New York et sur les jours fériés](./media/tutorial-data-analyst/8.png)

Cette fois, nous voulons mettre en évidence le nombre de courses de taxi pendant les jours fériés. À cet effet, nous choisissons **none** pour la colonne **Category**, et **rides_per_day** et **holiday** comme colonnes **Legend (series)** .

![Graphique en nuage de points présentant le nombre de courses de taxi pendant les jours fériés](./media/tutorial-data-analyst/9.png)

À partir du graphique en nuage de points, vous pouvez voir que pendant les jours fériés, le nombre de courses de taxi est plus faible. Il existe toujours une forte chute inexpliquée le 23 janvier. Vérifions la météo de ce jour-là à New York en interrogeant le jeu de données sur les données météorologiques :

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Visualisation des résultats du jeu de données sur les données météorologiques](./media/tutorial-data-analyst/10.png)

Les résultats de la requête indiquent que la chute du nombre de courses de taxi était liée aux raisons suivantes :

- Il y avait du blizzard ce jour-là à New-York, avec de fortes chutes de neige (environ 30 cm).
- Il faisait froid (la température était inférieure à zéro degré Celsius).
- Il y avait du vent (environ 10 m/s).

Ce tutoriel vous a montré comment un analyste de données peut rapidement effectuer une analyse exploratoire des données, combiner facilement différents jeux de données à l’aide de SQL à la demande et visualiser les résultats à l’aide d’Azure Synapse Studio.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment connecter SQL à la demande à Power BI Desktop et créer des rapports, consultez [Connecter SQL à la demande à Power BI Desktop et créer des rapports](tutorial-connect-power-bi-desktop.md).
 
