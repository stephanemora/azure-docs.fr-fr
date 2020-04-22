---
title: Créer et utiliser des vues dans SQL à la demande (préversion)
description: Dans cette section, vous allez apprendre à créer et à utiliser des vues pour wrapper des requêtes SQL à la demande (préversion). Les vues vous permettent de réutiliser ces requêtes. Elles sont également nécessaires si vous souhaitez utiliser des outils, tels que Power BI, conjointement avec SQL à la demande.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420773"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Créer et utiliser des vues dans SQL à la demande (préversion) avec Azure Synapse Analytics

Dans cette section, vous allez apprendre à créer et à utiliser des vues pour wrapper des requêtes SQL à la demande (préversion). Les vues vous permettent de réutiliser ces requêtes. Elles sont également nécessaires si vous souhaitez utiliser des outils, tels que Power BI, conjointement avec SQL à la demande.

## <a name="prerequisites"></a>Prérequis

La première étape consiste à passer en revue les articles ci-dessous pour vérifier que vous respectez les prérequis relatifs à la création et à l’utilisation de vues SQL à la demande :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Composants requis](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Créer une vue

Vous pouvez créer des vues de la même façon que vous créez des vues SQL Server standard. La requête ci-dessous crée une vue qui lit le fichier *population.csv*.

> [!NOTE]
> Modifiez la première ligne de la requête, c’est-à-dire [mydbname], afin d’utiliser la base de données que vous avez créée. Si vous n’avez pas créé de base de données, lisez [Première configuration](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
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

## <a name="use-a-view"></a>Utiliser une vue

Vous pouvez utiliser des vues dans vos requêtes, de la même façon que vous utilisez des vues dans les requêtes SQL Server.

La requête suivante illustre l’utilisation de la vue *population_csv* que nous avons créée à la section [Créer une vue](#create-a-view). Elle retourne les noms des pays avec leur population en 2019, dans l’ordre décroissant.

> [!NOTE]
> Modifiez la première ligne de la requête, c’est-à-dire [mydbname], afin d’utiliser la base de données que vous avez créée. Si vous n’avez pas créé de base de données, lisez [Première configuration](query-data-storage.md#first-time-setup).

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
