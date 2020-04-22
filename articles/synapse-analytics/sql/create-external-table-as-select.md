---
title: Stocker les résultats des requêtes dans le stockage
description: Dans cet article, vous allez voir comment stocker les résultats des requêtes dans le stockage en utilisant SQL à la demande (préversion).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421643"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Stocker les résultats de la requête dans le stockage en utilisant SQL à la demande (préversion) avec Azure Synapse Analytics

Dans cet article, vous allez voir comment stocker les résultats des requêtes dans le stockage en utilisant SQL à la demande (préversion).

## <a name="prerequisites"></a>Prérequis

La première étape consiste à consulter les articles ci-dessous et à veiller à suivre les prérequis :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Composants requis](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

Vous pouvez utiliser l’instruction CREATE EXTERNAL TABLE AS SELECT (CETAS) pour stocker les résultats des requêtes dans le stockage.

> [!NOTE]
> Modifiez la première ligne de la requête, par exemple [mydbname], afin d’utiliser la base de données que vous avez créée. Si vous n’avez pas créé de base de données, lisez [Première configuration](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://showdemoweu.dfs.core.windows.net/data/population_csv/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Utiliser une table externe existante

Vous pouvez utiliser une table externe créée via CETAS comme n’importe quelle table externe.

> [!NOTE]
> Modifiez la première ligne de la requête, par exemple [mydbname], afin d’utiliser la base de données que vous avez créée. Si vous n’avez pas créé de base de données, lisez [Première configuration](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’interroger différents types de fichiers, reportez-vous aux articles [Interroger un fichier CSV](query-single-csv-file.md), [Interroger des fichiers Parquet](query-parquet-files.md) et [Interroger des fichiers JSON](query-json-files.md).
