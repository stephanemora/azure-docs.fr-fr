---
title: Interroger des types imbriqués Parquet à l’aide de SQL à la demande (préversion)
description: Dans cet article, vous allez apprendre à interroger des types imbriqués Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427577"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Interroger des types imbriqués Parquet à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article explique comment écrire une requête à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics.  Cette requête va lire des types imbriqués Parquet.

## <a name="prerequisites"></a>Prérequis

Avant de lire le reste de cet article, consultez les articles suivants :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Composants requis](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Projeter des données imbriquées ou répétées

La requête suivante lit le fichier *justSimpleArray.parquet*. Il projette toutes les colonnes du fichier Parquet, y compris les données imbriquées ou répétées.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Accéder aux éléments à partir de colonnes imbriquées

La requête suivante lit le fichier *structExample.parquet* et montre comment exposer les éléments d’une colonne imbriquée :

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
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
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
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
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Étapes suivantes

L’article suivant vous indiquera comment [interroger des fichiers JSON](query-json-files.md).
