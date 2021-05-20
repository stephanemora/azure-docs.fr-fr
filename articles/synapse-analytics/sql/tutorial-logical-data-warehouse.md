---
title: 'Tutoriel : Utiliser un pool SQL serverless pour créer un entrepôt de données logique'
description: Ce tutoriel vous montre comment créer facilement un entrepôt de données logique sur des sources de données Azure en utilisant un pool SQL serverless.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/28/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: aba837ab590ae941e161e10e88782dcce944c085
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108760460"
---
# <a name="tutorial-create-logical-data-warehouse-with-serverless-sql-pool"></a>Tutoriel : Créer un entrepôt de données logique avec un pool SQL serverless

Dans ce tutoriel, vous allez apprendre à créer un entrepôt de données logique par-dessus le stockage Azure et Azure Cosmos DB.

Un entrepôt de données logique est une couche relationnelle créée par-dessus des sources de données Azure, comme Azure Data Lake Storage (ADLS), le stockage analytique Azure Cosmos DB ou le stockage Blob Azure.

## <a name="create-an-ldw-database"></a>Créer une base de données d’entrepôt de données logique

Vous avez besoin de créer une base de données personnalisée dans laquelle vous allez stocker vos tables et vues externes qui font référence à des sources de données externes.

```sql
CREATE DATABASE Ldw
      COLLATE Latin1_General_100_BIN2_UTF8;
```

Ce classement va fournir les performances optimales lors de la lecture de Parquet et Cosmos DB. Si vous ne voulez pas spécifier le classement de la base de données, veillez à spécifier ce classement dans la définition de la colonne.

## <a name="configure-data-sources-and-formats"></a>Configurer des sources de données et des formats

Pour commencer, vous avez besoin de configurer une source de données et de spécifier le format de fichier des données stockées à distance.

### <a name="create-data-source"></a>Créer une source de données

Les sources de données représentent des informations de chaîne de connexion qui décrivent l’emplacement où vos données sont placées et la manière de vous authentifier auprès de votre source de données.

Voici un exemple de définition de source de données qui fait référence à un [jeu de données Azure ouvert et public, fourni par le Centre européen de prévention et de contrôle des maladies (ECDC), sur les cas de COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) :

```sql
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/'
);
```

Un appelant peut accéder à une source de données sans informations d’identification si le propriétaire de cette source de données a autorisé l’accès anonyme ou octroyé un accès explicite à l’identité Azure AD de cet appelant.

Vous pouvez définir explicitement des informations d’identification personnalisées à utiliser pour accéder à des données sur une source de données externe.
- [Identité managée](develop-storage-files-storage-access-control.md?tabs=managed-identity) dans l’espace de travail Synapse
- [Signature d’accès partagé](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) du stockage Azure
- Clé de compte Cosmos DB en lecture seule qui vous permet de lire le stockage analytique Cosmos DB.

Un prérequis vous oblige à créer une clé principale dans la base de données :
```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Setup you password - you need to create master key only once';
```

Dans la source de données externe suivante, le pool Synapse SQL doit utiliser une identité managée de l’espace de travail pour accéder aux données du stockage.

```sql
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/',
    CREDENTIAL = WorkspaceIdentity
);
```

Pour accéder à un stockage analytique Cosmos DB, vous devez définir des informations d’identification contenant une clé de compte Cosmos DB en lecture seule.

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
```

### <a name="define-external-file-formats"></a>Définir des formats de fichiers externes

Les formats de fichiers externes définissent la structure des fichiers stockés sur une source de données externe. Vous pouvez définir des formats de fichiers externes Parquet et CSV :

```sql
CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
GO
CREATE EXTERNAL FILE FORMAT CsvFormat WITH (  FORMAT_TYPE = CSV );
```

## <a name="explore-your-data"></a>Exploration de vos données

Une fois que vous avez configuré vos sources de données, vous pouvez utiliser la fonction `OPENROWSET` pour explorer vos données. La fonction [OPENROWSET](develop-openrowset.md) lit le contenu d’une source de données distante (par exemple, un fichier) et retourne ce contenu sous la forme d’un ensemble de lignes.

```sql
select top 10  *
from openrowset(bulk 'latest/ecdc_cases.parquet',
                data_source = 'ecdc_cases'
                format='parquet') as a
```

La fonction `OPENROWSET` vous donne des informations sur la colonne dans les fichiers ou conteneurs externes et vous permet de définir un schéma de vos tables et vues externes.

## <a name="create-external-tables-on-azure-storage"></a>Créer des tables externes sur le stockage Azure

Une fois que vous avez découvert le schéma, vous pouvez créer des tables et vues externes sur vos sources de données externes. Une bonne pratique consiste à organiser vos tables et vues dans des schémas de la base de données. Dans la requête suivante, vous pouvez créer un schéma dans lequel vous allez placer tous les objets qui accèdent au jeu de données ECDC COVID dans Azure Data Lake Storage :

```sql
create schema ecdc_adls;
```

Les schémas de la base de données s’avèrent utiles pour regrouper les objets et définir des autorisations par schéma. 

Une fois que vous avez défini les schémas, vous pouvez créer des tables externes qui font référence aux fichiers.
La table externe suivante fait référence au fichier Parquet ECDC COVID placé dans le stockage Azure :

```sql
create external table ecdc_adls.cases (
    date_rep                   date,
    day                        smallint,
    month                      smallint,
    year                       smallint,
    cases                      smallint,
    deaths                     smallint,
    countries_and_territories  varchar(256),
    geo_id                     varchar(60),
    country_territory_code     varchar(16),
    pop_data_2018              int,
    continent_exp              varchar(32),
    load_date                  datetime2(7),
    iso_country                varchar(16)
) with (
    data_source= ecdc_cases,
    location = 'latest/ecdc_cases.parquet',
    file_format = ParquetFormat
);
```

Veillez à utiliser les plus petits types possibles pour les colonnes de type chaîne et nombre afin d’optimiser les performances de vos requêtes.

## <a name="create-views-on-azure-cosmos-db"></a>Créer des vues sur Azure Cosmos DB

Comme alternative aux tables externes, vous pouvez créer des vues par-dessus vos données externes. 

Comme les tables présentées dans l’exemple précédent, vous devez placer les vues dans des schémas distincts :

```sql
create schema ecdc_cosmosdb;
```

Vous pouvez maintenant créer une vue dans le schéma qui fait référence à un conteneur Cosmos DB :

```sql
CREATE OR ALTER VIEW ecdc_cosmosdb.Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) WITH
     ( date_rep varchar(20), 
       cases bigint,
       geo_id varchar(6) 
     ) as rows
```

Pour optimiser les performances, vous devez utiliser les plus petits types possibles dans la définition de schéma `WITH`.

> [!NOTE]
> Vous devez placer votre clé de compte Azure Cosmos DB dans des informations d’identification distinctes et faire référence à ces dernières à partir de la fonction `OPENROWSET`.
> Ne conservez pas votre clé de compte dans la définition de la vue.

## <a name="access-and-permissions"></a>Accès et autorisations

Pour finir, vous devez créer des utilisateurs de base de données en mesure d’accéder à votre entrepôt de données logique, puis leur octroyer des autorisations de sélectionner des données dans les tables et vues externes.
Dans le script suivant, vous pouvez voir comment ajouter un nouvel utilisateur et fournir des autorisations de lecture des données :

```sql
CREATE USER [jovan@contoso.com] FROM EXTERNAL PROVIDER;
GO
DENY ADMINISTER DATABASE BULK OPERATIONS TO [jovan@contoso.com]
GO
GRANT SELECT ON SCHEMA::ecdc_adls TO [jovan@contoso.com]
GO
GRANT SELECT ON OBJECT::ecdc_cosmosDB.cases TO [jovan@contoso.com]
GO
GRANT REFERENCES ON CREDENTIAL::MyCosmosDbAccountCredential TO [jovan@contoso.com]
GO
```

Les règles de sécurité dépendent de vos stratégies de sécurité. Voici quelques recommandations générales :
- Vous devez refuser l’autorisation `ADMINISTER DATABASE BULK OPERATIONS` aux nouveaux utilisateurs, car ceux-ci doivent être en mesure de lire des données uniquement en utilisant les tables et vues externes que vous avez préparées.
- Vous devez fournir une autorisation `SELECT` uniquement aux tables que les utilisateurs doivent être en mesure d’utiliser.
- Si vous fournissez un accès aux données à l’aide des vues, vous devez octroyer l’autorisation `REFERENCES` aux informations d’identification utilisées pour accéder à la source de données externe.

Cet utilisateur dispose des autorisations minimales nécessaires pour interroger les données externes. Si vous souhaitez créer un utilisateur avec pouvoir qui peut configurer des autorisations, des tables et des vues externes, vous pouvez lui accorder l’autorisation `CONTROL` :

```sql
GRANT CONTROL TO [jovan@contoso.com]
```

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment connecter un pool SQL serverless à Power BI Desktop et créer des rapports, consultez [Connecter un pool SQL serverless à Power BI Desktop et créer des rapports](tutorial-connect-power-bi-desktop.md).
- Pour découvrir comment utiliser des tables externes dans un pool SQL serverless, consultez [Utiliser des tables externes avec Synapse SQL](develop-tables-external-tables.md?tabs=sql-pool).

