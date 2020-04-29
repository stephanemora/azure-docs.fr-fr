---
title: Interroger plusieurs dossiers et fichiers csv à l’aide de SQL à la demande (préversion)
description: SQL à la demande (préversion) prend en charge la lecture de plusieurs fichiers ou dossiers en utilisant des caractères génériques similaires à ceux utilisés dans le système d’exploitation Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457363"
---
# <a name="query-folders-and-multiple-csv-files"></a>Interroger plusieurs dossiers et fichiers CSV  

Cet article explique comment écrire une requête à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics.

SQL à la demande prend en charge la lecture de plusieurs fichiers ou dossiers en utilisant des caractères génériques similaires à ceux utilisés dans le système d’exploitation Windows. Toutefois, il offre davantage de flexibilité, car l’utilisation de plusieurs caractères génériques est autorisée.

## <a name="prerequisites"></a>Prérequis

Avant de lire le reste de cet article, veillez à consulter les articles suivants :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Composants requis](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Lire plusieurs fichiers dans un dossier

Vous allez utiliser le dossier *csv/taxi* pour suivre les exemples de requêtes. Il contient les données d’enregistrements de courses des taxis jaunes de New York couvrant la période de juillet 2016 à juin 2018.

Les fichiers contenus dans le dossier *csv/taxi* sont nommées d’après l’année et le mois :

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

La structure de chaque fichier est la suivante :
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Lire tous les fichiers dans le dossier
    
L’exemple ci-dessous lit tous les fichiers de données NYC Yellow Taxi figurant dans le dossier *csv/taxi*, et retourne le nombre total de passagers et de trajets par an. Il montre également l’utilisation de fonctions d’agrégation.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tous les fichiers accessibles avec la fonction OPENROWSET seule doivent présenter la même structure (c’est-à-dire les mêmes nombre de colonnes et types de données).

### <a name="read-subset-of-files-in-folder"></a>Lire un sous-ensemble de fichiers dans le dossier

L’exemple ci-dessous lit les fichiers de données NYC Yellow Taxi pour 2017 dans le dossier *csv/taxi* en utilisant un caractère générique, et retourne le montant total par type de paiement.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Tous les fichiers accessibles avec la fonction OPENROWSET seule doivent présenter la même structure (c’est-à-dire les mêmes nombre de colonnes et types de données).

## <a name="read-folders"></a>Lire les dossiers

Le chemin d’accès que vous fournissez à la fonction OPENROWSET peut également être celui d’un dossier. Les sections suivantes incluent le types de requêtes suivants.

### <a name="read-all-files-from-specific-folder"></a>Lire tous les fichiers d’un dossier spécifique

Vous pouvez lire tous les fichiers figurant dans un dossier en utilisant le caractère générique de niveau fichier, comme indiqué dans [Lire tous les fichiers dans le dossier](#read-all-files-in-folder). Il existe cependant un moyen d’interroger un dossier et d’utiliser tous les fichiers qu’il contient.

Si le chemin d’accès fourni dans la fonction OPENROWSET pointe vers un dossier, tous les fichiers figurant dans celui-ci sont utilisés comme source pour votre requête. La requête suivante lit tous les fichiers figurant dans le dossier *csv/taxi*.

> [!NOTE]
> Notez la présence de la barre oblique (/) à la fin du chemin d’accès dans la requête ci-dessous. Elle indique qu’il s’agit d’un dossier. En cas d’omission de la barre oblique (/), la requête cible un fichier nommé *taxi* à la place.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tous les fichiers accessibles avec la fonction OPENROWSET seule doivent présenter la même structure (c’est-à-dire les mêmes nombre de colonnes et types de données).

### <a name="read-all-files-from-multiple-folders"></a>Lire tous les fichiers de plusieurs dossiers

Vous pouvez lire tous les fichiers figurant dans plusieurs dossiers en utilisant un caractère générique. La requête suivante lit tous les fichiers de tous les dossiers figurant dans le dossier *csv*, dont les noms commencent par *t* et se terminent par *i*.

> [!NOTE]
> Notez la présence de la barre oblique (/) à la fin du chemin d’accès dans la requête ci-dessous. Elle indique qu’il s’agit d’un dossier. En cas d’omission de la barre oblique (/), la requête cible les fichiers nommés *t&ast;i* à la place.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tous les fichiers accessibles avec la fonction OPENROWSET seule doivent présenter la même structure (c’est-à-dire les mêmes nombre de colonnes et types de données).

Étant donné qu’un seul dossier correspond aux critères, le résultat de la requête est le même que celui obtenu avec l’option [Lire tous les fichiers dans le dossier](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Plusieurs caractères génériques

Vous pouvez utiliser plusieurs caractères génériques sur différents niveaux de chemin d’accès. Par exemple, vous pouvez enrichir la requête précédente afin de lire uniquement les fichiers contenant des données de 2017 dans tous les dossiers dont le nom commence par *t* et se termine par *i*.

> [!NOTE]
> Notez la présence de la barre oblique (/) à la fin du chemin d’accès dans la requête ci-dessous. Elle indique qu’il s’agit d’un dossier. En cas d’omission de la barre oblique (/), la requête cible les fichiers nommés *t&ast;i* à la place.
> Le nombre maximal de caractères génériques par requête est limité à 10.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Tous les fichiers accessibles avec la fonction OPENROWSET seule doivent présenter la même structure (c’est-à-dire les mêmes nombre de colonnes et types de données).

Étant donné qu’un seul dossier correspond aux critères, le résultat de la requête est le même que celui obtenu avec les options [Lire un sous-ensemble de fichiers dans un dossier](#read-subset-of-files-in-folder) et [Lire tous les fichiers d’un dossier spécifique](#read-all-files-from-specific-folder). Des scénarios d’utilisation de caractères génériques plus complexes sont traités dans [Interroger des fichiers Parquet](query-parquet-files.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez l’article [Interroger des fichiers spécifiques](query-specific-files.md).
