---
title: Créer et utiliser des vues dans SQL à la demande (préversion)
description: Dans cette section, vous allez apprendre à créer et à utiliser des vues pour wrapper des requêtes SQL à la demande (préversion). Les vues vous permettent de réutiliser ces requêtes. Elles sont également nécessaires si vous souhaitez utiliser des outils, tels que Power BI, conjointement avec SQL à la demande.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: ca60b7c12ec7e7a5e04202e377c345055ce1090c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736005"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Créer et utiliser des vues dans SQL à la demande (préversion) avec Azure Synapse Analytics

Dans cette section, vous allez apprendre à créer et à utiliser des vues pour wrapper des requêtes SQL à la demande (préversion). Les vues vous permettent de réutiliser ces requêtes. Elles sont également nécessaires si vous souhaitez utiliser des outils, tels que Power BI, conjointement avec SQL à la demande.

## <a name="prerequisites"></a>Prérequis

La première étape consiste à créer une base de données dans laquelle la vue sera créée et à initialiser les objets nécessaires pour s’authentifier sur le stockage Azure en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur cette base de données. Toutes les requêtes de cet article seront exécutées sur votre exemple de base de données.

## <a name="create-a-view"></a>Créer une vue

Vous pouvez créer des vues de la même façon que vous créez des vues SQL Server standard. La requête suivante crée une vue qui lit le fichier *population.csv*.

> [!NOTE]
> Modifiez la première ligne de la requête, c’est-à-dire [mydbname], afin d’utiliser la base de données que vous avez créée.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

La vue de cet exemple utilise la fonction `OPENROWSET` qui se sert du chemin absolu des fichiers sous-jacents. Si vous disposez de `EXTERNAL DATA SOURCE` avec une URL racine de votre stockage, vous pouvez utiliser `OPENROWSET` avec `DATA_SOURCE` et le chemin relatif du fichier :

```
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Utiliser une vue

Vous pouvez utiliser des vues dans vos requêtes, de la même façon que vous utilisez des vues dans les requêtes SQL Server.

La requête suivante illustre l’utilisation de la vue *population_csv* que nous avons créée à la section [Créer une vue](#create-a-view). Elle retourne les noms des pays/régions avec leur population en 2019, dans l’ordre décroissant.

> [!NOTE]
> Modifiez la première ligne de la requête, c’est-à-dire [mydbname], afin d’utiliser la base de données que vous avez créée.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’interroger différents types de fichiers, reportez-vous aux articles [Interroger un fichier CSV](query-single-csv-file.md), [Interroger des fichiers Parquet](query-parquet-files.md) et [Interroger des fichiers JSON](query-json-files.md).
