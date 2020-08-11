---
title: Importer et exporter des données entre des pools Spark (préversion) et des pools SQL
description: Cet article explique comment utiliser le connecteur personnalisé pour déplacer des données entre des pools SQL et des pools Spark (préversion).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 7b02296d5d9aed5866c0efcdf85fa1c9946617d0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501897"
---
# <a name="introduction"></a>Introduction

Le connecteur Azure Synapse Apache Spark-Synapse SQL est conçu pour transférer efficacement des données entre des pools Spark (préversion) et des pools SQL dans Azure Synapse. Il fonctionne uniquement sur les pools SQL, et non avec SQL à la demande.

## <a name="design"></a>Conception

Le transfert de données entre les pools Spark et les pools SQL peut être effectué à l’aide de JDBC. Toutefois, avec deux systèmes distribués tels que les pools SQL et Spark, JDBC tend à être un goulot d’étranglement lors du transfert de données en série.

Le connecteur pool Azure Synapse Apache Spark-Synapse SQL est une implémentation de source de données pour Apache Spark. Il utilise Azure Data Lake Storage Gen2 et PolyBase dans les pools SQL pour transférer efficacement des données entre le cluster Spark et l’instance Synapse SQL.

![Architecture du connecteur](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Authentification dans Azure Synapse Analytics

L’authentification entre systèmes est rendue transparente dans Azure Synapse Analytics. Il existe un service de jeton qui se connecte à Azure Active Directory pour obtenir des jetons de sécurité à utiliser lors de l’accès au compte de stockage ou au serveur de l’entrepôt de données.

Pour cette raison, il n’est pas nécessaire de créer des informations d’identification ou de les spécifier dans l’API du connecteur, tant que l’authentification AAD est configurée sur le compte de stockage et le serveur de l’entrepôt de données. Si ce n’est pas le cas, l’authentification SQL peut être spécifiée. Pour plus d’informations, consultez la section [Utilisation](#usage).

## <a name="constraints"></a>Contraintes

- Ce connecteur fonctionne uniquement dans Scala.

## <a name="prerequisites"></a>Prérequis

- Être membre du rôle **db_exporter** dans la base de données/le pool SQL vers ou à partir duquel vous souhaitez transférer des données.
- Être membre du rôle Contributeur aux données Blob du stockage sur le compte de stockage par défaut.

Pour créer des utilisateurs, connectez-vous à la base de données de pool SQL et suivez les exemples suivants :

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Pour attribuer un rôle :

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Usage

Les instructions d’importation ne sont pas obligatoires ; elles sont pré-importées pour l’expérience de notebook.

### <a name="transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Transférer des données vers ou depuis un pool SQL attaché à l’espace de travail

> [!NOTE]
> **Importations non nécessaires dans l’expérience de notebook**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API Lire

```scala
val df = spark.read.sqlanalytics("<DBName>.<Schema>.<TableName>")
```

L’API ci-dessus fonctionne pour les tables internes (gérées) et externes dans le pool SQL.

#### <a name="write-api"></a>API d’écriture

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

L’API d’écriture crée la table dans le pool SQL, puis appelle Polybase pour charger les données.  La table ne doit pas exister dans le pool SQL, sinon une erreur est retournée indiquant qu’il existe déjà un objet nommé.

Valeurs TableType

- Constants.INTERNAL : table managée dans le pool SQL
- Constants.EXTERNAL : table externe dans le pool SQL

Table managée dans le pool SQL

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Table externe dans le pool SQL

Pour écrire dans une table externe dans le pool SQL, une EXTERNAL DATA SOURCE et un EXTERNAL FILE FORMAT doivent exister dans le pool SQL.  Pour plus d’informations, consultez [Création d’une source de données externe](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) et de [formats de fichiers externes](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dans le pool SQL.  Vous trouverez ci-dessous des exemples de création d’une source de données externe et de formats de fichiers externes dans le pool SQL.

```sql
--For an external table, you need to pre-create the data source and file format in SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

Un objet EXTERNAL CREDENTIAL n’est pas nécessaire lors de l’utilisation de l’authentification directe Azure Active Directory auprès du compte de stockage.  Assurez-vous que vous êtes membre du rôle « Contributeur aux données Blob du stockage » sur le compte de stockage.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="if-you-transfer-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Si vous transférez des données vers ou depuis un pool ou une base de données SQL en dehors de l’espace de travail

> [!NOTE]
> Importations non nécessaires dans l’expérience de notebook

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API Lire

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API d’écriture

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-aad"></a>Utiliser l’authentification SQL au lieu d’AAD

#### <a name="read-api"></a>API Lire

Actuellement, le connecteur ne prend pas en charge l’authentification par jeton pour un pool SQL extérieur à l’espace de travail. Vous devez utiliser l’authentification SQL.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API d’écriture

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-the-pyspark-connector"></a>Utiliser le connecteur PySpark

> [!NOTE]
> Cet exemple est fourni avec uniquement l’expérience de notebook à l’esprit.

Supposez que vous avez un tableau « pyspark_df » que vous souhaitez écrire dans le DW.

Créez une table temporaire à l’aide du dataframe dans PySpark :

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Exécutez une cellule Scala dans le notebook PySpark à l’aide de magics :

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

De même, dans le scénario de lecture, lisez les données à l’aide de Scala et écrivez-les dans une table temporaire, puis utilisez Spark SQL dans PySpark pour interroger la table temp dans un dataframe.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Permettre à d’autres utilisateurs d’utiliser le connecteur Azure Synapse Apache Spark - Synapse SQL dans votre espace de travail

Vous devez être propriétaire des données blob du Stockage sur le compte de stockage ADLS Gen2 connecté à l’espace de travail pour modifier les autorisations manquantes d’autres personnes. Veillez à ce que l’utilisateur ait accès à l’espace de travail et dispose de l’autorisation d’exécuter des notebooks.

### <a name="option-1"></a>Option 1 :

- Rendre l’utilisateur Propriétaire/Contributeur aux données Blob du stockage

### <a name="option-2"></a>Option 2 :

- Spécifiez les listes ACL suivantes sur la structure de dossiers :

| Dossier | / | synapse | workspaces  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Autorisations d’accès | --X | --X | --X | --X | --X | --X | -WX |
| Autorisations par défaut | ---| ---| ---| ---| ---| ---| ---|

- Vous devriez pouvoir mettre sur liste ACL tous les dossiers à partir de « synapse » et vers le bas à partir du Portail Azure. Pour mettre sur liste ACL le dossier racine « / », suivez les instructions ci-dessous.

- Connectez-vous au compte de stockage connecté à l’espace de travail à partir de l’Explorateur Stockage à l’aide d’AAD.
- Sélectionnez votre compte et indiquez l’URL ADLS Gen2 et le système de fichiers par défaut de l’espace de travail.
- Lorsque le compte de stockage apparaît, cliquez avec le bouton droit sur l’espace de travail de liste, puis sélectionnez « Gérer l’accès ».
- Ajoutez l’utilisateur au dossier / avec l’autorisation d’accès « Exécuter ». Sélectionnez « OK ».

> [!IMPORTANT]
> Veillez à ne pas sélectionner « Par défaut » si ce n’est pas votre intention.

## <a name="next-steps"></a>Étapes suivantes

- [Création d’un pool SQL à l’aide du Portail Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Création d’un pool Apache Spark à l’aide du Portail Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
