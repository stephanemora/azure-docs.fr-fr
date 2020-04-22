---
title: Créer et utiliser des tables externes dans SQL à la demande (préversion)
description: Dans cette section, vous allez apprendre à créer et à utiliser des tables externes dans SQL à la demande (préversion). Les tables externes sont utiles lorsque vous souhaitez contrôler l’accès aux données externes dans SQL à la demande et si vous souhaitez utiliser des outils, tels que Power BI, conjointement avec SQL à la demande.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420793"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Créer et utiliser des tables externes dans SQL à la demande (préversion) avec Azure Synapse Analytics

Dans cette section, vous allez apprendre à créer et à utiliser des tables externes dans SQL à la demande (préversion). Les tables externes sont utiles lorsque vous souhaitez contrôler l’accès aux données externes dans SQL à la demande et si vous souhaitez utiliser des outils, tels que Power BI, conjointement avec SQL à la demande.

## <a name="prerequisites"></a>Prérequis

La première étape consiste à consulter les articles ci-dessous pour vérifier que vous respectez les prérequis relatifs à la création et à l’utilisation de tables externes SQL à la demande :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Prérequis](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Créer une table externe

Vous pouvez créer des tables externes de la même façon que vous créez habituellement des tables externes SQL Server. La requête ci-dessous crée une table externe qui lit le fichier *population.csv*.

> [!NOTE]
> Modifiez la première ligne de la requête, par exemple [mydbname], afin d’utiliser la base de données que vous avez créée. Si vous n’avez pas créé de base de données, lisez [Première configuration](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Utiliser une table externe

Vous pouvez utiliser des tables externes dans vos requêtes de la même façon que vous les utilisez dans les requêtes SQL Server.

La requête suivante illustre l’utilisation de la table externe *population* que nous avons créée dans la section [Créer une table externe](#create-an-external-table). Elle retourne les noms des pays avec leur population en 2019, dans l’ordre décroissant.

> [!NOTE]
> Modifiez la première ligne de la requête, par exemple [mydbname], afin d’utiliser la base de données que vous avez créée. Si vous n’avez pas créé de base de données, lisez [Première configuration](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage des résultats d’une requête dans le stockage, consultez [Stocker les résultats d’une requête dans le stockage](../sql/create-external-table-as-select.md).
