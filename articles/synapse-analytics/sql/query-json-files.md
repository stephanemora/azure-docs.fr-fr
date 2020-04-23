---
title: Interroger des fichiers JSON à l’aide de SQL à la demande (préversion)
description: Cette section explique comment lire des fichiers JSON à l’aide de SQL à la demande dans Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 645baf9102785d223fd1f23ae52a4609725f795b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770805"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Interroger des fichiers JSON à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article explique comment écrire une requête à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics. L’objectif de la requête est de lire des fichiers JSON.

## <a name="prerequisites"></a>Prérequis

Avant de lire le reste de cet article, consultez les articles suivants :

- [Première configuration](query-data-storage.md#first-time-setup)
- [Composants requis](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Exemples de fichiers JSON

La section ci-dessous contient des exemples de scripts pour lire les fichiers JSON. Les fichiers sont stockés dans un dossier *books* au sein d’un conteneur *json*, et contiennent une entrée de livre unique avec la structure suivante :

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>Lire des fichiers JSON

Pour traiter des fichiers JSON à l’aide de JSON_VALUE et de [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), vous devez lire le fichier JSON à partir du stockage sous la forme d’une colonne unique. Le script suivant lit le fichier *book1.json* en tant que colonne unique :

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Vous lisez l’intégralité du fichier JSON sous la forme d’une seule ligne ou colonne. Ainsi, FIELDTERMINATOR, FIELDQUOTE et ROWTERMINATOR sont définis sur 0x0b.

## <a name="query-json-files-using-json_value"></a>Interroger des fichiers JSON à l’aide de JSON_VALUE

La requête suivante montre comment utiliser [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour récupérer des valeurs scalaires (titre, éditeur) à partir d’un livre intitulé *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles* :

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Interroger des fichiers JSON à l’aide de JSON_QUERY

La requête suivante montre comment utiliser [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour récupérer des objets et des tableaux (auteurs) à partir d’un livre intitulé *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics* :

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Interroger des fichiers JSON à l’aide de OPENJSON

La requête suivante utilise [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Elle récupère des objets et des propriétés dans un livre intitulé *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles* :

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants de cette série vont vous montrer comment exécuter les tâches suivantes :

- [Interrogation de plusieurs dossiers et fichiers](query-folders-multiple-csv-files.md)
- [Créer et utiliser des vues](create-use-views.md)
