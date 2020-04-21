---
title: Utiliser des métadonnées de fichier dans les requêtes
description: La fonction OPENROWSET fournit des informations de fichier et de chemin d’accès sur chaque fichier utilisé dans la requête pour filtrer ou analyser des données en fonction du nom de fichier ou du chemin d’accès du dossier.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427545"
---
# <a name="using-file-metadata-in-queries"></a>Utiliser des métadonnées de fichier dans les requêtes

Le service de requête à la demande SQL peut traiter plusieurs fichiers et dossiers, comme décrit dans l’article [Interroger plusieurs dossiers et fichiers](query-folders-multiple-csv-files.md). Dans cet article, vous allez apprendre à utiliser des informations de métadonnées sur des noms de fichiers et de dossiers dans les requêtes.

Vous devrez peut-être parfois savoir quelle source de fichier ou de dossier est corrélée à une ligne spécifique dans le jeu de résultats.

Vous pouvez utiliser les fonctions `filepath` et `filename` pour retourner les noms ou le chemin d’accès des fichiers dans le jeu de résultats. Vous pouvez les utiliser pour filtrer des données sur le nom de ou le chemin d’accès du dossier. Ces fonctions sont décrites dans la section syntaxe des fonctions [filename](develop-storage-files-overview.md#filename-function) et [filepath](develop-storage-files-overview.md#filepath-function). Vous trouverez ci-dessous de brèves descriptions des exemples.

## <a name="prerequisites"></a>Prérequis

Avant de lire le reste de cet article, consultez les conditions préalables suivants :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Composants requis](query-data-storage.md#prerequisites)

## <a name="functions"></a>Fonctions

### <a name="filename"></a>Nom de fichier

Cette fonction retourne le nom du fichier sont provient la ligne.

L’exemple suivant lit les fichiers de données NYC Yellow Taxi pour les trois derniers mois de 2017, et retourne le nombre de courses par fichier. La partie OPENROWSET de la requête spécifie les fichiers à lire.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

L’exemple suivant montre comment utiliser *filename()* dans la clause WHERE pour filtrer les fichiers à lire. Il accède au dossier entier dans la partie OPENROWSET de la requête et filtre les fichiers dans la clause WHERE.

Vos résultats seront les mêmes que dans l’exemple précédent.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

La fonction filepath retourne un chemin d’accès complet ou partiel :

- En cas d’appel sans paramètre, elle retourne le chemin complet du fichier dont provient la ligne.
- En cas d’appel avec paramètre, elle retourne une partie du chemin qui correspond au caractère générique occupant la position spécifiée dans le paramètre. Par exemple, la valeur de paramètre 1 retourne une partie du chemin qui correspond au premier caractère générique.

L’exemple suivant lit les fichiers de données NYC Yellow Taxi pour les trois derniers mois de 2017. Elle retourne le nombre de courses par chemin d’accès de fichier. La partie OPENROWSET de la requête spécifie les fichiers à lire.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

L’exemple suivant montre comment utiliser *filepath()* dans la clause WHERE pour filtrer les fichiers à lire.

Vous pouvez utiliser les caractères génériques dans la partie OPENROWSET de la requête et filtrer les fichiers dans la clause WHERE. Vos résultats seront les mêmes que dans l’exemple précédent.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Étapes suivantes

L’article suivant explique comment [interroger des fichiers Parquet](query-parquet-files.md).
