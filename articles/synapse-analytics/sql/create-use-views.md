---
title: Créer et utiliser des vues dans le pool SQL serverless
description: Dans cette section, vous allez découvrir comment créer et utiliser des vues pour wrapper des requêtes de pool SQL serverless. Les vues vous permettent de réutiliser ces requêtes. Elles sont également nécessaires si vous voulez utiliser des outils comme Power BI en combinaison avec un pool SQL serverless.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2f6bfda53dc1e15488960dd72e8e71c88a82ab64
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013134"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Créer et utiliser des vues avec le pool SQL serverless dans Azure Synapse Analytics

Dans cette section, vous allez découvrir comment créer et utiliser des vues pour wrapper des requêtes de pool SQL serverless. Les vues vous permettent de réutiliser ces requêtes. Elles sont également nécessaires si vous voulez utiliser des outils comme Power BI en combinaison avec un pool SQL serverless.

## <a name="prerequisites"></a>Prérequis

La première étape consiste à créer une base de données dans laquelle la vue sera créée et à initialiser les objets nécessaires pour s’authentifier sur le stockage Azure en exécutant le [script d’installation](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) sur cette base de données. Toutes les requêtes de cet article seront exécutées sur votre exemple de base de données.

## <a name="views-over-external-data"></a>Vues de données externes

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

La vue utilise `EXTERNAL DATA SOURCE` avec une URL racine de votre stockage, en tant que `DATA_SOURCE` et ajoute un chemin de fichier relatif aux fichiers.

### <a name="delta-lake-views"></a>Vues de Delta Lake

Si vous créez les vues pour un dossier Delta Lake, vous devez spécifier l’emplacement du dossier racine après l’option `BULK` au lieu de spécifier le chemin d’accès du fichier.

> [!div class="mx-imgBorder"]
>![Dossier ECDC COVID-19 Delta Lake](./media/shared/covid-delta-lake-studio.png)

La fonction `OPENROWSET` qui lit les données du dossier Delta Lake examine la structure de celui-ci et identifie automatiquement les emplacements des fichiers.

```sql
create or alter view CovidDeltaLake
as
select *
from openrowset(
           bulk 'covid',
           data_source = 'DeltaLakeStorage',
           format = 'delta'
    ) with (
           date_rep date,
           cases int,
           geo_id varchar(6)
           ) as rows
```

Consultez les problèmes connus dans la [page d’aide relative aux pools SQL serverless Synapse](resources-self-help-sql-on-demand.md#delta-lake).

## <a name="partitioned-views"></a>Vues partitionnées

Si vous disposez d’un ensemble de fichiers partitionnés dans la structure de dossiers hiérarchique, vous pouvez décrire le modèle de partition à l’aide des caractères génériques dans le chemin du fichier. Utilisez la fonction `FILEPATH` pour exposer des parties du chemin de dossier en tant que colonnes de partitionnement.

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

Les vues partitionnées effectuent une élimination des partitions de dossier si vous interrogez cette vue avec les filtres sur les colonnes de partitionnement. Cela peut améliorer les performances de vos requêtes.

### <a name="delta-lake-partitioned-views"></a>Vues partitionnées de Delta Lake

Si vous créez les vues partitionnées pour un stockage Delta Lake, vous pouvez spécifier uniquement un dossier Delta Lake. Vous n’avez pas besoin d’exposer explicitement les colonnes de partitionnement à l’aide de la fonction `FILEPATH` :

```sql
CREATE OR ALTER VIEW YellowTaxiView
AS SELECT *
FROM  
    OPENROWSET(
        BULK 'yellow',
        DATA_SOURCE = 'DeltaLakeStorage',
        FORMAT='DELTA'
    ) nyc
```

La fonction `OPENROWSET` examine la structure du dossier Delta Lake sous-jacent, puis identifie et expose automatiquement les colonnes de partitionnement. L’élimination des partitions est effectuée automatiquement si vous placez la colonne de partitionnement dans la clause `WHERE` d’une requête.

Le nom de dossier dans la fonction `OPENROWSET` (`yellow` dans cet exemple), qui est concaténé avec l’URI `LOCATION` défini dans la source de données `DeltaLakeStorage` doit référencer le dossier Delta Lake racine contenant un sous-dossier nommé `_delta_log`.

> [!div class="mx-imgBorder"]
>![Dossier Yellow Taxi Delta Lake](./media/shared/yellow-taxi-delta-lake.png)

Consultez les problèmes connus sur la [page d’aide relative aux pools SQL serverless Synapse](resources-self-help-sql-on-demand.md#delta-lake).

## <a name="json-views"></a>Vues JSON

Les vues constituent un bon choix si vous devez réaliser un traitement supplémentaire du jeu de résultats récupéré à partir des fichiers. Par exemple, il peut s’agir d’analyser des fichiers JSON auxquels nous devons appliquer les fonctions JSON afin d’extraire les valeurs des documents JSON :

```sql
CREATE OR ALTER VIEW CovidCases
AS 
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
```

La fonction `OPENJSON` analyse chaque ligne du fichier JSON qui contient un document JSON par ligne au format textuel.

## <a name="cosmosdb-view"></a>Vue CosmosDB

Les vues peuvent être créées dans les conteneurs Azure CosmosDB si le stockage analytique CosmosDB est activé pour les conteneurs. Le nom du compte CosmosDB, le nom de la base de données et le nom du conteneur doivent être ajoutés à la vue, et la clé d’accès en lecture seule doit être placée dans les informations d’identification de la base de données qui sont référencées par la vue.

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Pour plus d’informations sur l’[interrogation des conteneurs CosmosDB, consultez Synapse Link ici](query-cosmos-db-analytical-store.md).

## <a name="use-a-view"></a>Utiliser une vue

Vous pouvez utiliser des vues dans vos requêtes, de la même façon que vous utilisez des vues dans les requêtes SQL Server.

La requête suivante illustre l’utilisation de la vue *population_csv* que nous avons créée à la section [Créer une vue](#views-over-external-data). Elle retourne les noms des pays/régions avec leur population en 2019, dans l’ordre décroissant.

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
