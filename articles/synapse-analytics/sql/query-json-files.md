---
title: Interroger des fichiers JSON à l’aide de SQL à la demande (préversion)
description: Cette section explique comment lire des fichiers JSON à l’aide de SQL à la demande dans Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7a8c9083ecbadbf63cf0ac65dc1803b478e939fe
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873405"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Interroger des fichiers JSON à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article explique comment écrire une requête à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics. L’objectif de la requête est de lire des fichiers JSON. Les formats pris en charge sont listés dans [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Prérequis

La première étape consiste à **créer la base de données** dans laquelle seront exécutées les requêtes. Ensuite, initialisez les objets en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur cette base de données. Ce script crée les sources de données, les informations d’identification étendues à la base de données et les formats de fichiers externes utilisés dans ces exemples.

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
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

La requête suivante montre comment utiliser [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour récupérer des valeurs scalaires (titre, éditeur) à partir d’un livre intitulé *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics* :

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

La requête suivante utilise [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Elle récupère des objets et des propriétés dans un livre intitulé *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics* :

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants de cette série vont vous montrer comment exécuter les tâches suivantes :

- [Interrogation de plusieurs dossiers et fichiers](query-folders-multiple-csv-files.md)
- [Créer et utiliser des vues](create-use-views.md)
