---
title: 'Tutoriel : Chargement depuis Azure Data Lake Storage Gen1 vers Azure SQL Data Warehouse| Microsoft Docs'
description: Utilisez des tables externes PolyBase pour charger des données Azure Data Lake Storage Gen1 dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c3902061264b75ba177ba150176d784ad5384a9f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297194"
---
# <a name="load-data-from-azure-data-lake-storage-gen1-to-sql-data-warehouse"></a>Chargement de données Azure Data Lake Storage Gen1 dans SQL Data Warehouse
Utilisez des tables externes PolyBase pour charger des données Azure Data Lake Storage Gen1 dans Azure SQL Data Warehouse. Même si vous pouvez exécuter des requêtes ad hoc sur des données stockées dans Data Lake Storage Gen1, nous vous recommandons d’importer les données dans SQL Data Warehouse pour de meilleures performances.

> [!div class="checklist"]
> * Créez les objets de base de données à charger à partir de Data Lake Storage Gen1.
> * Connectez-vous à un répertoire Data Lake Storage Gen1.
> * Charger des données dans Azure SQL Data Warehouse.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer
Avant de commencer ce didacticiel, téléchargez et installez la dernière version de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Une application Azure Active Directory pour l’authentification de service à service. Pour créer, suivez la procédure [Authentification Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Vous avez besoin de l’ID client, de la clé et de la valeur du point de terminaison du jeton OAuth2.0 de votre application Active Directory pour vous connecter à votre compte Data Lake Storage Gen1 depuis SQL Data Warehouse. Pour savoir comment obtenir ces valeurs, cliquez sur le lien ci-dessus. Pour l’inscription à l’application Azure Active Directory : utilisez l’ID d’application en tant qu’ID de client.
> 

* Un entrepôt de données SQL Azure. Consultez [Créer et interroger un entrepôt de données SQL Azure](create-data-warehouse-portal.md).

* Compte Data Lake Storage Gen1. Consultez [Prise en main d’Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Créer des informations d’identification
Pour accéder à votre compte Data Lake Storage Gen1, vous devez créer une clé principale de base de données afin de chiffrer les informations secrètes d’identification au cours de l’étape suivante. Créez ensuite des informations d’identification incluses dans l’étendue de la base de données, qui stockent les informations d’identification du principal du service configurées dans AAD. Pour ceux d'entre vous qui ont utilisé PolyBase pour se connecter à des objets blob Windows Azure Storage, notez que la syntaxe des informations d’identification est différente.

Pour vous connecter à Data Lake Storage Gen1, vous devez **tout d’abord** créer une application Azure Active Directory, créer une clé d’accès et accorder l’accès aux applications à la ressource Data Lake Storage Gen1. Pour obtenir des instructions, consultez [S’authentifier auprès de Data Lake Storage Gen1 en utilisant Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Créer la source de données externe
Utilisez cette commande [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) pour stocker l’emplacement des données. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage Gen1.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorageGen1
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSG1Credential
);
```

## <a name="configure-data-format"></a>Configurer le format des données
Pour importer les données depuis Data Lake Storage Gen1, vous devez spécifier le format de fichier externe. Cet objet définit la manière dont les fichiers sont écrits dans Data Lake Storage Gen1.
Pour obtenir la liste complète, consultez notre documentation T-SQL sur [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Créer les tables externes
Maintenant que vous avez spécifié la source des données et le format de fichier, vous êtes prêt à créer les tables externes. Les tables externes vous permettent d’interagir avec des données externes. Le paramètre d’emplacement peut spécifier un fichier ou un répertoire. Si il spécifie un répertoire, tous les fichiers contenus dans le répertoire seront chargés.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage Gen1 root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorageGen1
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considérations relatives aux tables externes
La création d’une table externe est facile, mais il existe quelques nuances qui doivent être abordées.

Les tables externes sont fortement typés. Cela signifie que chaque ligne de données reçue doit respecter la définition du schéma de la table.
Si une ligne ne respecte pas la définition du schéma, cette ligne est exclue du chargement.

Les options REJECT_TYPE et REJECT_VALUE vous permettent de définir le nombre de lignes ou le pourcentage de données à inclure dans la table finale. Au cours du chargement, si la valeur à rejeter est atteinte, le chargement échoue. La principale cause de rejet de lignes est une incompatibilité de définition de schéma. Par exemple, si une colonne reçoit par erreur le schéma int alors que les données dans le fichier représentent une chaîne, aucune ligne ne sera chargée.

Data Lake Storage Gen1 utilise le contrôle d’accès en fonction du rôle (RBAC) pour contrôler l’accès aux données. Cela signifie que le principal de service doit disposer des autorisations de lecture pour les répertoires définis dans le paramètre d’emplacement, ainsi que pour les enfants des fichiers et du répertoire final. Cela permet à PolyBase d’authentifier et de charger ces données. 

## <a name="load-the-data"></a>Chargement des données
Pour charger des données depuis Data Lake Storage Gen1, utilisez l’instruction [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse). 

CTAS crée une table et la remplit avec les résultats d’une instruction select. CTAS définit la nouvelle table de manière à proposer les mêmes colonnes et les mêmes types de données que les résultats de l’instruction select. Si vous sélectionnez toutes les colonnes d’une table externe, la nouvelle table est un réplica des colonnes et des types de données dans la table externe.

Dans cet exemple, nous créons une table de hachage distribuée appelée DimProduct à partir de notre table externe DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimiser la compression columnstore
Par défaut, SQL Data Warehouse stocke la table comme un index columnstore en cluster. Après un chargement, certaines lignes de données peuvent ne pas être compressées dans le columnstore.  Cela peut être dû à diverses raisons. Pour plus d’informations, consultez [Gérer les index Columnstore](sql-data-warehouse-tables-index.md).

Pour optimiser les performances des requêtes et la compression du columnstore après un chargement, reconstruisez la table afin de forcer l’index columnstore à compresser toutes les lignes.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimiser les statistiques
Il est préférable de créer des statistiques sur une colonne immédiatement après un chargement. Les statistiques offrent plusieurs possibilités. Par exemple, si vous créez des statistiques sur une colonne pour chaque colonne, il faudra peut-être beaucoup de temps pour reconstruire toutes les statistiques. S’il est certain que des colonnes ne se trouveront pas dans les prédicats de requête, vous pouvez ignorer la création des statistiques sur ces colonnes.

Si vous décidez de créer des statistiques sur une colonne pour chaque colonne de chaque table, vous pouvez utiliser l’exemple de code de procédure stockée `prc_sqldw_create_stats` dans l’article portant sur les [statistiques](sql-data-warehouse-tables-statistics.md).

L’exemple suivant est un bon point de départ pour la création de statistiques. Il permet de créer des statistiques sur une colonne pour chaque colonne de la table de dimension, et chaque colonne de jointure des tables de faits. Vous pouvez toujours ajouter ultérieurement des statistiques sur une ou plusieurs colonnes dans d’autres colonnes de table de faits.

## <a name="achievement-unlocked"></a>Et voilà !
Vous avez correctement chargé les données dans Azure SQL Data Warehouse. Bon travail !

## <a name="next-steps"></a>Étapes suivantes 
Dans ce tutoriel, vous avez créé des tables externes pour définir la structure des données stockées dans Data Lake Storage Gen1, puis vous avez utilisé l’instruction PolyBase CREATE TABLE AS SELECT pour charger des données dans votre entrepôt de données. 

Voici les étapes que vous avez effectuées :
> [!div class="checklist"]
> * Création des objets de base de données à charger à partir de Data Lake Storage Gen1.
> * Connexion à un répertoire Data Lake Storage Gen1.
> * Chargement de données dans Azure SQL Data Warehouse.
> 

Le chargement des données est la première étape du développement d’une solution d’entreposage des données à l’aide de SQL Data Warehouse. Découvrez nos ressources de développement.

> [!div class="nextstepaction"]
>[Apprendre à développer des tables dans SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




