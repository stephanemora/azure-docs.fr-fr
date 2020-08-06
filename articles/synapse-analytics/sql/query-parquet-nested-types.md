---
title: Interroger des types imbriqués Parquet à l’aide de SQL à la demande (préversion)
description: Dans cet article, vous allez apprendre à interroger des types imbriqués Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386603"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Interroger des types imbriqués dans des fichiers Parquet et JSON à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article explique comment écrire une requête à l’aide de SQL à la demande (préversion) dans Azure Synapse Analytics. Cette requête va lire des types imbriqués Parquet.
Les types imbriqués sont des structures complexes qui représentent des objets ou des tableaux. Les types imbriqués peuvent être stockés dans : 
- [PARQUET](query-parquet-files.md) où vous pouvez avoir plusieurs colonnes complexes contenant des tableaux et des objets.
- [Fichiers JSON](query-json-files.md) hiérarchiques où vous pouvez lire des documents JSON complexes en tant que colonne unique.
- Collection CosmosDB où chaque document peut contenir des propriétés imbriquées complexes (actuellement en préversion publique contrôlée).

Synapse SQL à la demande met en forme tous les types imbriqués en tant qu’objets et tableaux JSON. Vous pouvez ainsi [extraire ou modifier des objets complexes à l’aide de fonctions JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) ou [analyser des données JSON à l’aide de la fonction OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Vous trouverez ci-dessous un exemple de requête qui extrait des valeurs scalaires et d’objets du fichier de jeu de données JSON [COVID-19 Open Research Dataset](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) avec des objets imbriqués. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

La fonction `JSON_VALUE` retourne une valeur scalaire à partir du champ dans le chemin d’accès spécifié. La fonction `JSON_QUERY` retourne un objet au format JSON à partir du champ dans le chemin d’accès spécifié.

> [!IMPORTANT]
> Cet exemple utilise un fichier à partir du jeu de données [COVID-19 Open Research Dataset](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Consultez la licence et la structure des données sur cette page.

## <a name="prerequisites"></a>Prérequis

La première étape consiste à **créer une base de données** avec une source de données qui y fait référence. Ensuite, initialisez les objets en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur cette base de données. Ce script d’installation crée les sources de données, les informations d’identification délimitées à la base de données et les formats de fichiers externes utilisés dans ces exemples.

## <a name="project-nested-or-repeated-data"></a>Projeter des données imbriquées ou répétées

Le fichier PARQUET peut avoir plusieurs colonnes avec des types complexes. Les valeurs de ces colonnes sont mises en forme en tant que texte JSON et retournées en tant que colonne VARCHAR. La requête suivante lit le fichier *structExample.parquet* et montre comment lire les valeurs des colonnes imbriquées : 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Cette requête retourne le résultat suivant où le contenu de chaque objet imbriqué est retourné sous forme de texte JSON :

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date":"2009-04-25"}| {"Time":"20:51:54.3598000"}|    {"Timestamp":"5501-04-08 12:13:57.4821000"}|    {"Decimal":11143412.25350}| {"Float":0.5}|
|{"Date":"1916-04-29"}| {"Time":"00:16:04.6778000"}|    {"Timestamp":"1990-06-30 20:50:52.6828000"}|    {"Decimal":1963545.62800}|  {"Float":-2.125}|

La requête suivante lit le fichier *justSimpleArray.parquet*. Il projette toutes les colonnes du fichier Parquet, y compris les données imbriquées ou répétées.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Cette requête retournera le résultat suivant :

|SimpleArray|
| --- |
|[11,12,13]|
|[21,22,23]|

## <a name="read-properties-from-nested-object-columns"></a>Lire les propriétés à partir des colonnes d’objets imbriqués

Le fonction `JSON_VALUE` vous permet de retourner des valeurs à partir d’une colonne au format texte JSON :

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Le résultat est présenté dans le tableau suivant :

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Informations supplémentaires Un éco-épidémiolo... | Julien   | - Figure S1 : Phylogénie de... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Contrairement aux fichiers JSON, qui, dans la plupart des cas, retournent une seule colonne contenant un objet JSON complexe, les fichiers PARQUET peuvent avoir plusieurs objets complexes. Vous pouvez lire les propriétés de la colonne imbriquée à l’aide de la fonction `JSON_VALUE` sur chaque colonne. `OPENROWSET` vous permet de spécifier directement les chemins d’accès des propriétés imbriquées dans la clause `WITH`. Les chemins d’accès peuvent être définis en tant que nom de la colonne, ou vous pouvez ajouter l’[expression de chemin d’accès JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) après le type de colonne.

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
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
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

Le résultat est présenté dans le tableau suivant :

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11,12,13] | 11   | 12 | 13 |
| [21,22,23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Accéder à des sous-objets à partir de colonnes complexes

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

Vous pouvez également référencer explicitement les colonnes que vous souhaitez renvoyer dans la clause `WITH` :

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

La structure `MapOfPersons` est retournée en tant que colonne `VARCHAR` et mise en forme en tant que chaîne JSON.

## <a name="projecting-values-from-repeated-columns"></a>Projection de valeurs à partir de colonnes répétées

Si vous avez un tableau de valeurs scalaires (par exemple `[1,2,3]`) dans certaines colonnes, vous pouvez facilement les développer et les joindre à la ligne principale à l’aide du script suivant :

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Étapes suivantes

L’article suivant vous indiquera comment [interroger des fichiers JSON](query-json-files.md).
