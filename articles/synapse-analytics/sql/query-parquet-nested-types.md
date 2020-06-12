---
title: Interroger des types imbriqués Parquet à l’aide de SQL à la demande (préversion)
description: Dans cet article, vous allez apprendre à interroger des types imbriqués Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d5a10e3fe2803c7b9a10abe9bf959a694030cc8c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235435"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Interroger des types imbriqués Parquet à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article explique comment écrire une requête à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics.  Cette requête va lire des types imbriqués Parquet.

## <a name="prerequisites"></a>Prérequis

La première étape consiste à **créer une base de données** avec une source de données qui y fait référence. Ensuite, initialisez les objets en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur cette base de données. Ce script d’installation crée les sources de données, les informations d’identification délimitées à la base de données et les formats de fichiers externes utilisés dans ces exemples.

## <a name="project-nested-or-repeated-data"></a>Projeter des données imbriquées ou répétées

La requête suivante lit le fichier *justSimpleArray.parquet*. Il projette toutes les colonnes du fichier Parquet, y compris les données imbriquées ou répétées.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Accéder aux éléments à partir de colonnes imbriquées

La requête suivante lit le fichier *structExample.parquet* et montre comment exposer les éléments d’une colonne imbriquée. Vous pouvez référencer une valeur imbriquée de deux façons :
- Spécification de l’expression de chemin d’accès de valeur imbriquée après la spécification de type.
- La mise en forme du nom de colonne en tant que chemin d’accès imbriqué à l’aide de «.» pour référencer les champs.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original nested columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateValue] DATE '$.DateStruct.Date',
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Accéder aux éléments à partir de colonnes répétées

La requête suivante lit le fichier *justSimpleArray.parquet* et utilise [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour récupérer un élément **scalaire** à partir d’une colonne répétée, comme un tableau ou une carte :

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

La requête suivante lit le fichier *mapExample.parquet* et utilise [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour récupérer un élément **non scalaire** à partir d’une colonne répétée, comme un tableau ou une carte :

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Étapes suivantes

L’article suivant vous indiquera comment [interroger des fichiers JSON](query-json-files.md).
