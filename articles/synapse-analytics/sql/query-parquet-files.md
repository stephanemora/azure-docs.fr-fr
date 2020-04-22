---
title: Interroger des fichiers Parquet à l’aide de SQL à la demande (préversion)
description: Cet article vous explique comment interroger des fichiers Parquet à l’aide de SQL à la demande (préversion).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427589"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Interroger des fichiers Parquet à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article vous explique comment écrire une requête à l’aide de SQL à la demande (préversion) pour lire des fichiers Parquet.

## <a name="prerequisites"></a>Prérequis

Avant de lire le reste de cet article, consultez les articles suivants :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Composants requis](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Dataset

Vous pouvez interroger les fichiers Parquet de la même façon que vous lisez des fichiers CSV. La seule différence réside dans le fait que le paramètre FILEFORMAT doit être défini sur PARQUET. Les exemples de cet article montrent les spécificités de lecture des fichiers Parquet.

> [!NOTE]
> Vous n’avez pas besoin de spécifier de colonnes dans la clause OPENROWSET WITH lors de la lecture de fichiers Parquet. SQL à la demande utilise les métadonnées dans le fichier Parquet et lie les colonnes par nom.

Vous allez utiliser le dossier *parquet/taxi* pour les exemples de requêtes. Il contient les données d’enregistrements de courses des taxis jaunes de New York couvrant la période de juillet 2016 à juin 2018.

Les données sont partitionnées par année et par mois, et la structure des dossiers se présente comme suit :

- year=2016
  - month=6
  - ...
  - month=12
- year=2017
  - month=1
  - ...
  - month=12
- year=2018
  - month=1
  - ...
  - month=6

## <a name="query-set-of-parquet-files"></a>Ensemble de requêtes de fichiers Parquet

Vous pouvez spécifier uniquement les colonnes intéressantes lorsque vous interrogez des fichiers Parquet.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Inférence de schéma automatique

Vous n’avez pas besoin d'utiliser la clause OPENROWSET WITH lors de la lecture de fichiers Parquet. Les noms de colonnes et les types de données sont automatiquement lus à partir des fichiers Parquet.

L’exemple ci-dessous montre les fonctionnalités d’inférence automatique du schéma des fichiers Parquet. Il renvoie le nombre de lignes en septembre 2017 sans spécifier de schéma.

> [!NOTE]
> Vous n’avez pas besoin de spécifier de colonnes dans la clause OPENROWSET WITH lors de la lecture de fichiers Parquet. Dans ce cas, le service de requête SQL à la demande utilise les métadonnées dans le fichier Parquet et lie les colonnes par nom.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Interroger des données partitionnées

Le jeu de données fourni dans cet exemple est divisé (partitionné) en sous-dossiers distincts. Utilisez la fonction filepath pour cibler des partitions spécifiques. Cet exemple montre les montants par année, par mois et par type de paiement pour les trois premiers mois de 2017.

> [!NOTE]
> La requête SQL à la demande est compatible avec le schéma de partitionnement Hive/Hadoop.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Mappage des types

Les fichiers Parquet contiennent des descriptions de type pour chaque colonne. Le tableau suivant explique comment les types Parquet sont mappés aux types SQL natifs.

| Type Parquet | Type logique Parquet (annotation) | Type de données SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(classement UTF8) |
| BINARY |STRING |varchar \*(classement UTF8) |
| BINARY |ENUM|varchar \*(classement UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |Décimal |
| BINARY |JSON |varchar (max) \*(classement UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Décimal |
| BYTE_ARRAY |INTERVAL |varchar (max), sérialisé au format standardisé |
| INT32 |INT(8, true) |SMALLINT |
| INT32 |INT(16, true) |SMALLINT |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |TINYINT |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |Date |
| INT32 |DECIMAL |Décimal |
| INT32 |TIME (MILLIS )|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false ) |decimal(20,0) |
| INT64 |DECIMAL |Décimal |
| INT64 |TIME (MICROS / NANOS) |time |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[Type complexe](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Liste |varchar(max), sérialisé en JSON |
|[Type complexe](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max), sérialisé en JSON |

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment [interroger des types imbriqués Parquet](query-parquet-nested-types.md).
