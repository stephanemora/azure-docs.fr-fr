---
title: Stocker des résultats de requêtes à partir d’un pool SQL serverless
description: Dans cet article, vous allez découvrir comment stocker des résultats de requêtes dans un stockage en utilisant le pool SQL serverless.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 93a8a4d2e7f4f8a455f6437c69dee4c44c2e29f3
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109751920"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Stocker des résultats de requêtes en utilisant le pool SQL serverless dans Azure Synapse Analytics

Dans cet article, vous allez découvrir comment stocker des résultats de requêtes dans un stockage en utilisant le pool SQL serverless.

## <a name="prerequisites"></a>Prérequis

La première étape consiste à **créer la base de données** dans laquelle seront exécutées les requêtes. Ensuite, initialisez les objets en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur cette base de données. Ce script d’installation crée les sources de données, les informations d’identification limitées à la base de données et les formats de fichiers externes utilisés pour la lecture des données dans ces exemples.

Suivez les instructions de cet article pour créer des sources de données, des informations d’identification limitées à la base de données et des formats de fichiers externes utilisés pour l’écriture des données dans le stockage de sortie.

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

Vous pouvez utiliser l’instruction CREATE EXTERNAL TABLE AS SELECT (CETAS) pour stocker les résultats des requêtes dans le stockage.

> [!NOTE]
> Modifiez la première ligne de la requête, c’est-à-dire [mydbname], afin d’utiliser la base de données que vous avez créée.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
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
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

> [!NOTE]
> Vous devez modifier ce script et changer l’emplacement cible pour le réexécuter. Les tables externes ne peuvent pas être créées à l’emplacement où vous avez déjà des données.

## <a name="use-the-external-table"></a>Utiliser la table externe

Vous pouvez utiliser la table externe créée via CETAS comme n’importe quelle table externe.

> [!NOTE]
> Modifiez la première ligne de la requête, c’est-à-dire [mydbname], afin d’utiliser la base de données que vous avez créée.

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

## <a name="remarks"></a>Notes

Une fois vos résultats stockés, vous ne pouvez pas modifier les données dans la table externe. Vous ne pouvez pas répéter ce script, car CETAS ne remplace pas les données sous-jacentes créées lors de l’exécution précédente. Votez pour les éléments de commentaires suivants si certains d’entre eux sont requis dans vos scénarios, ou proposez-en d’autres sur le site de commentaires Azure :
- [Activer l’insertion de nouvelles données dans une table externe](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/32981347-polybase-allow-insert-new-data-to-existing-exteran)
- [Activer la suppression des données de la table externe](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/15158034-polybase-delete-from-external-tables)
- [Spécifier des partitions dans CETAS](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/19520860-polybase-partitioned-by-functionality-when-creati)
- [Spécifier une taille de fichier et un nombre de fichiers](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/42263617-cetas-specify-number-of-parquet-files-file-size)

Les seuls types de sortie pris en charge sont Parquet et CSV. Vous pouvez voter pour les autres types sur le [site de commentaires Azure](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’interroger différents types de fichiers, consultez les articles [Interroger un fichier CSV](query-single-csv-file.md), [Interroger des fichiers Parquet](query-parquet-files.md) et [Interroger des fichiers JSON](query-json-files.md).
