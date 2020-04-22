---
title: Utilisation de SQL à la demande (préversion)
description: Dans ce guide de démarrage rapide, vous allez voir et découvrir à quel point il est facile d’interroger différents types de fichiers en utilisant SQL à la demande (préversion).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0d543abc88c1e45f2c1f5503473d8e92566fc582
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457380"
---
# <a name="quickstart-using-sql-on-demand"></a>Démarrage rapide : Utilisation de SQL à la demande

SQL Synapse à la demande (préversion) est un service de requête serverless qui vous permet d’exécuter les requêtes SQL sur vos fichiers placés dans le Stockage Azure. Dans ce guide de démarrage rapide, vous allez apprendre à interroger différents types de fichiers en utilisant SQL à la demande.

Les types de fichiers suivants sont pris en charge : JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Prérequis

Choisissez le client SQL avec lequel vous souhaitez émettre les requêtes :

- [Azure Synapse Studio](quickstart-synapse-studio.md) est un outil web que vous pouvez utiliser pour parcourir les fichiers dans le stockage et créer une requête SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) est un outil client qui vous permet d’exécuter des requêtes SQL et des notebooks sur votre base de données à la demande.
- [SQL Server Management Studio](sql/get-started-ssms.md) est un outil client qui vous permet d’exécuter des requêtes SQL sur votre base de données à la demande.

Paramètres pour le guide de démarrage rapide :

| Paramètre                                 | Description                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresse de point de terminaison de service SQL à la demande    | Utilisée comme nom de serveur                                   |
| Région de point de terminaison de service SQL à la demande     | Utilisée pour déterminer le stockage que nous allons utiliser dans les exemples |
| Nom d’utilisateur et mot de passe pour l’accès au point de terminaison | Utilisés pour accéder au point de terminaison                               |
| Base de données utilisée pour créer des vues         | Base de données utilisée comme point de départ dans les exemples       |

## <a name="first-time-setup"></a>Première configuration

Avant d’utiliser les exemples, effectuez les opérations suivantes :

- Créer une base de données pour vos vues (au cas où vous souhaiteriez utiliser des vues)
- Créer les informations d’identification que devra utiliser SQL à la demande pour accéder aux fichiers dans le stockage

### <a name="create-database"></a>Créer une base de données

Créez votre propre base de données à des fins de démonstration. Il s’agit de la base de données dans laquelle vous créez vos vues. Utilisez cette base de données dans les exemples de requêtes fournis dans cet article.

> [!NOTE]
> Les bases de données sont utilisées uniquement pour les métadonnées de vue, et non pour les données réelles.
>
> Notez le nom de base de données que vous utilisez, car vous en aurez besoin plus loin dans le Guide de démarrage rapide.

Utilisez la requête suivante, en remplaçant `mydbname` par le nom de votre choix :

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Créer des informations d’identification

Pour exécuter des requêtes à l’aide de SQL à la demande, créez les informations d’identification dont devra se servir SQL à la demande pour accéder aux fichiers dans le stockage.

> [!NOTE]
> Notez que vous devez créer des informations d’identification pour accéder au compte de stockage. Bien que SQL à la demande puisse accéder aux stockages de différentes régions, le fait d’avoir le stockage et l’espace de travail Azure Synapse dans la même région offre de meilleures performances.

Modifiez l’extrait de code suivant afin de créer des informations d’identification pour les conteneurs CSV, JSON et Parquet :

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>Interrogation des fichiers CSV

L’image suivante est un aperçu du fichier à interroger :

![10 premières lignes du fichier CSV sans en-tête, avec caractères de nouvelle ligne de style Windows.](./sql/media/query-single-csv-file/population.png)

La requête suivante montre comment lire un fichier CSV qui ne contient pas de ligne d’en-tête et comprend des caractères de nouvelle ligne de style Windows et des colonnes délimitées par des virgules :

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Vous pouvez spécifier le schéma au moment de la compilation de la requête.
Pour plus d’exemples, consultez la procédure indiquant comment [interroger des fichiers CSV](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Interrogation des fichiers Parquet

L’exemple suivant montre les fonctionnalités d’inférence automatique du schéma pour l’interrogation des fichiers Parquet. Il retourne le nombre de lignes en septembre 2017 sans spécifier de schéma.

> [!NOTE]
> Vous n’avez pas besoin de spécifier de colonnes dans la clause `OPENROWSET WITH` lors de la lecture de fichiers Parquet. Dans ce cas, SQL à la demande utilise les métadonnées dans le fichier Parquet et lie les colonnes par nom.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Pour plus d’informations sur l’interrogation des fichiers Parquet, cliquez [ici](sql/query-parquet-files.md).

## <a name="querying-json-files"></a>Interrogation des fichiers JSON

### <a name="json-sample-file"></a>Exemple de fichier JSON

Les fichiers sont stockés dans un dossier *books* au sein d’un conteneur *json*, et contiennent une entrée de livre unique avec la structure suivante :

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

### <a name="querying-json-files"></a>Interrogation des fichiers JSON

La requête suivante montre comment utiliser [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour récupérer des valeurs scalaires (titre, éditeur) à partir d’un livre intitulé *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles* :

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Nous lisons la totalité du fichier JSON en tant que ligne/colonne unique. Ainsi, FIELDTERMINATOR, FIELDQUOTE et ROWTERMINATOR sont définis sur 0x0B, car nous ne pensons pas les trouver dans le fichier.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant vous lancer dans la lecture des articles de guide de démarrage rapide suivants :

- [Interroger un fichier CSV](sql/query-single-csv-file.md)
- [Interroger des dossiers et plusieurs fichiers CSV](sql/query-folders-multiple-csv-files.md)
- [Interroger des fichiers spécifiques](sql/query-specific-files.md)
- [Interroger des fichiers Parquet](sql/query-parquet-files.md)
- [Interroger des types imbriqués Parquet](sql/query-parquet-nested-types.md)
- [Interroger des fichiers JSON](sql/query-json-files.md)
- [Création et utilisation de vues](sql/create-use-views.md)
- [Création et utilisation de tables externes](sql/create-use-external-tables.md)
- [Conserver le résultat d’une requête dans le stockage Azure](sql/create-external-table-as-select.md)

Passez à l’article suivant pour découvrir comment interroger un fichier CSV.
> [!div class="nextstepaction"]
> [Interroger un fichier CSV](sql/query-single-csv-file.md)
