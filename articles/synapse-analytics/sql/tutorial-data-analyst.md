---
title: Tutoriel sur l’analyste des données - Utiliser SQL à la demande (préversion) pour analyser des jeux de données Azure Open Datasets dans Azure Synapse Studio (préversion)
description: Dans ce tutoriel, vous allez apprendre à effectuer facilement des analyses de données exploratoires en combinant différents jeux de données Azure Open Datasets avec SQL à la demande (préversion) et à visualiser les résultats dans Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426278"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Utiliser SQL à la demande (préversion) pour analyser des jeux de données Azure Open Datasets et visualiser les résultats dans Azure Synapse Studio (préversion)

Dans ce tutoriel, vous apprenez à effectuer des analyses de données exploratoires en combinant différents jeux de données Azure Open Datasets avec SQL à la demande, puis en visualisant les résultats dans Azure Synapse Studio.

En particulier, vous analysez le [jeu de données sur les taxis de la ville de New York (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) qui inclut les dates/heures de début et de fin des trajets et les adresses correspondantes, la distance des trajets, les tarifs détaillés, les types de tarifs, les types de paiement et le nombre de passagers signalé par le conducteur.

L’analyse a pour but de trouver des tendances dans l’évolution du nombre de courses de taxi. Vous analysez deux autres jeux de données Azure Open Datasets ([Jours fériés](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) et [Données météorologiques](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) pour comprendre les valeurs hors norme du nombre de courses de taxi.

## <a name="create-credentials"></a>Créer des informations d’identification

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Inférence de schéma automatique

Les données étant stockées au format de fichier Parquet, l’inférence de schéma automatique est disponible. Il est donc possible d’interroger facilement les données sans avoir besoin de lister les types de données de toutes les colonnes dans les fichiers. De plus, il est possible d’utiliser le mécanisme de colonne virtuelle et la fonction filepath pour filtrer un certain sous-ensemble de fichiers.

Commençons par nous familiariser avec les données sur les taxis de la ville de New York en exécutant la requête suivante :

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Voici un extrait du résultat pour les données sur les taxis de la ville de New York :

![Extrait du résultat](./media/tutorial-data-analyst/1.png)

De même, nous pouvons interroger le jeu de données sur les jours fériés à l’aide de la requête suivante :

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Voici un extrait du résultat pour le jeu de données sur les jours fériés :

![extrait du résultat 2](./media/tutorial-data-analyst/2.png)

Enfin, nous pouvons également interroger le jeu de données météorologiques à l’aide de la requête suivante :

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Voici un extrait du résultat pour le jeu de données météorologiques :

![Extrait du résultat 3](./media/tutorial-data-analyst/3.png)

Pour en savoir plus sur la signification des différentes colonnes, consultez les descriptions des jeux de données [Taxis de la ville de New York](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Jours fériés](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) et [Données météorologiques](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analyse des séries chronologiques, de la saisonnalité et des valeurs hors norme

Vous pouvez facilement résumer le nombre annuel de courses de taxi à l’aide de la requête suivante :

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

Voici un extrait du résultat pour le nombre annuel de courses de taxi :

![Extrait du résultat 4](./media/tutorial-data-analyst/4.png)

Vous pouvez visualiser les données dans Synapse Studio en passant de la vue Table à la vue Graphique. Vous pouvez choisir parmi différents types de graphiques (aires, barres, histogrammes, courbes, secteurs et nuages de points). En l’occurrence, nous allons tracer un histogramme avec la colonne Category définie sur « current_year » :

![Visualisation des résultats 5](./media/tutorial-data-analyst/5.png)

Cette visualisation laisse clairement apparaître une tendance à la baisse du nombre de courses sur plusieurs années, vraisemblablement due à une augmentation récente du nombre d’entreprises de covoiturage.

> [!NOTE]
> Au moment de la rédaction de ce tutoriel, les données pour 2019 sont incomplètes, d’où une forte baisse du nombre de courses pour cette année.

À présent, concentrons notre analyse sur une seule année, par exemple, 2016. La requête suivante retourne le nombre quotidien de courses au cours de cette année :

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

Voici un extrait du résultat de cette requête :

![Extrait du résultat 6](./media/tutorial-data-analyst/6.png)

Là encore, nous pouvons facilement visualiser les données en traçant un histogramme avec la colonne Category définie sur « current_day » et la colonne Legend (series) définie sur « rides_per_day ».

![Visualisation des résultats 7](./media/tutorial-data-analyst/7.png)

Le tracé laisse apparaître un modèle hebdomadaire, que caractérise le pic du samedi. Pendant les mois d’été, qui correspondent à une période de vacances, il y a moins de courses de taxi. Toutefois, il existe également des chutes importantes du nombre de courses de taxi sans modèle clair de quand et pourquoi elles se produisent.

À présent, voyons si ces chutes sont susceptibles d’être corrélées avec les jours fériés en joignant le jeu de données sur les courses de taxis de la ville de New York au jeu de données sur les jours fériés :

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

![Visualisation des résultats 8](./media/tutorial-data-analyst/8.png)

Cette fois, nous souhaitons mettre en évidence le nombre de courses de taxi pendant les jours fériés. À cet effet, nous allons choisir « none » pour la colonne Category et « rides_per_day » et « holiday » comme colonnes Legend (series).

![Visualisation des résultats 9](./media/tutorial-data-analyst/9.png)

Le tracé laisse clairement apparaître que, pendant les jours fériés, le nombre de courses de taxi est plus faible. Toutefois, il existe toujours une chute énorme inexpliquée le 23 janvier. Vérifions la météo de ce jour à New York en interrogeant le jeu de données météorologiques :

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

![Visualisation des résultats 10](./media/tutorial-data-analyst/10.png)

Les résultats de la requête indiquent que la chute du nombre de courses de taxi était due au :

- Blizzard, avec de fortes chutes de neige (environ 30 cm)
- Froid (température inférieure à zéro degré Celsius)
- Vent (environ 10 m/s)

Ce tutoriel vous a montré comment l’analyste des données peut rapidement effectuer une analyse exploratoire des données, combiner facilement différents jeux de données à l’aide de SQL à la demande et visualiser les résultats avec Azure Synapse Studio.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment connecter SQL à la demande à Power BI Desktop et créer des rapports, consultez l’article [Connecter SQL à la demande à Power BI Desktop et créer un rapport](tutorial-connect-power-bi-desktop.md).
 
