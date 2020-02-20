---
title: Connecteur Spark avec Azure SQL Database et SQL Server
description: Découvrez comment utiliser le connecteur Spark pour Azure SQL Database et SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471650"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Accélérer l’analytique du Big Data en temps réel avec le connecteur Spark pour Azure SQL Database et SQL Server

Le connecteur Spark pour Azure SQL Database et SQL Server permet aux bases de données SQL, y compris à Azure SQL Database et SQL Server, de jouer le rôle de source de données d’entrée ou de récepteur de données de sortie pour les travaux Spark. Il vous permet d’utiliser des données transactionnelles en temps réel dans l’analytique du Big Data et de conserver les résultats pour des requêtes ad hoc ou des rapports. Par rapport au connecteur JDBC intégré, ce connecteur offre la possibilité d’insérer des données en bloc dans des bases de données SQL. Il peut donner de meilleurs résultats que l’insertion ligne par ligne et atteindre des performances entre 10 et 20 fois plus rapides. Le connecteur Spark pour Azure SQL Database et SQL Server prend également en charge l’authentification AAD. Il vous permet de vous connecter en toute sécurité à votre base de données Azure SQL depuis Azure Databricks à l’aide de votre compte AAD. Il fournit des interfaces similaires à celles du connecteur JDBC intégré. Effectuer la migration de vos travaux Spark existants pour utiliser ce nouveau connecteur est très simple.

## <a name="download"></a>Téléchargement
Pour commencer, téléchargez le connecteur Spark à SQL Database depuis le [dépôt azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) sur GitHub.

## <a name="official-supported-versions"></a>Versions officielles prises en charge

| Composant                            | Version                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 ou version ultérieure           |
| Scala                                | 2.10 ou version ultérieure            |
| Pilote Microsoft JDBC pour SQL Server | 6.2 ou version ultérieure             |
| Microsoft SQL Server                 | SQL Server 2008 ou version ultérieure |
| Azure SQL Database                   | Prise en charge                |

Le connecteur Spark pour Azure SQL Database et SQL Server utilise le pilote Microsoft JDBC pour SQL Server pour déplacer des données entre les nœuds de rôle de travail Spark et les bases de données SQL :
 
Le flux de données est le suivant :
1. Le nœud principal Spark se connecte à SQL Server ou à Azure SQL Database et charge les données à partir d’une table spécifique ou à l’aide d’une requête SQL spécifique.
2. Le nœud principal Spark distribue les données aux nœuds de rôle de travail à des fins de transformation. 
3. Le nœud de rôle de travail se connecte à SQL Server ou à Azure SQL Database et écrit les données dans la base de données. L’utilisateur peut choisir d’utiliser l’insertion ligne par ligne ou l’insertion en bloc.

Le diagramme suivant illustre le flux de données.

   ![architecture](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Générer le connecteur Spark à SQL Database
Actuellement, le projet de connecteur utilise maven. Pour créer le connecteur sans dépendances, vous pouvez exécuter :

- Un package propre mvn
- Les dernières versions du fichier JAR téléchargées à partir du dossier release
- Le fichier SQL DB Spark JAR inclus

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Connecter Spark à SQL Database à l’aide du connecteur
Vous pouvez vous connecter à Azure SQL Database ou à SQL Server à partir de travaux Spark, lire ou écrire des données. Vous pouvez également exécuter une requête DML ou DDL dans une base de données Azure SQL ou SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Lire des données à partir d’Azure SQL Database ou SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Lecture de données à partir d’Azure SQL Database ou SQL Server avec une requête SQL spécifiée
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Écrire des données dans Azure SQL Database ou SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Exécuter une requête DML ou DDL dans Azure SQL Database ou SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Connecter Spark à Azure SQL Database à l’aide de l’authentification AAD
Vous pouvez vous connecter à Azure SQL Database à l’aide de l’authentification Azure Active Directory (AAD). Utilisez l’authentification AAD pour gérer de manière centralisée les identités des utilisateurs de base de données et comme alternative à l’authentification SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Connexion à l’aide du mode d’authentification ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Configuration requise
Si vous utilisez le mode d’authentification ActiveDirectoryPassword, vous devez télécharger [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) et ses dépendances, puis les inclure dans le chemin de la build Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Connexion à l’aide du jeton d’accès
#### <a name="setup-requirement"></a>Configuration requise
Si vous utilisez le mode d’authentification par jeton d’accès, vous devez télécharger [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) et ses dépendances, puis les inclure dans le chemin de la build Java.

Consultez [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database](sql-database-aad-authentication.md) pour savoir comment obtenir un jeton d’accès à votre base de données Azure SQL.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Écrire des données dans Azure SQL Database ou SQL Server en utilisant l’insertion en bloc
Le connecteur JDBC traditionnel écrit les données dans la base de données Azure SQL ou SQL Server en utilisant l’insertion ligne par ligne. Vous pouvez utiliser le connecteur Spark à SQL Database pour écrire des données dans la base de données SQL en utilisant l’insertion en bloc. Ce connecteur améliore considérablement les performances d’écriture lors du chargement de grands jeux de données ou de données dans des tables où un index de banque des colonnes est utilisé.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Étapes suivantes
Si ce n’est déjà fait, téléchargez le connecteur Spark pour Azure SQL Database et SQL Server depuis le [dépôt GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) et parcourez les ressources supplémentaires qui s’y trouvent :

- [Exemples de bloc-notes Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Exemples de scripts (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Vous pouvez également consulter [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Guide sur Apache Spark SQL, les tableaux de données et les jeux de données) et la [documentation Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

