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
ms.openlocfilehash: 515fd9bfedc5bc5d3cefda2a357c351f515fb5f5
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194669"
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

- Disposer du rôle **db_exporter** dans la base de données/le pool SQL vers ou à partir duquel vous souhaitez transférer des données

Pour créer des utilisateurs, connectez-vous à la base de données et suivez les exemples suivants :

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Pour attribuer un rôle :

```sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Usage

Les instructions d’importation ne sont pas obligatoires ; elles sont pré-importées pour l’expérience de notebook.

### <a name="transferring-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Transfert de données vers ou à partir d’un pool SQL joint à l’espace de travail

> [!NOTE]
> **Importations non nécessaires dans l’expérience de notebook**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API Lire

```scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

L’API ci-dessus fonctionne pour les tables internes (gérées) et externes dans le pool SQL.

#### <a name="write-api"></a>API d’écriture

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

où TableType peut être Constants.INTERNAL ou Constants.EXTERNAL.

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

L’authentification auprès du stockage et du serveur SQL est effectuée.

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Si vous transférez des données vers ou à partir d’un pool ou d’une base de données SQL en dehors de l’espace de travail

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
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Utilisation de l’authentification SQL au lieu d’AAD

#### <a name="read-api"></a>API Lire

Actuellement, le connecteur ne prend pas en charge l’authentification par jeton pour un pool SQL extérieur à l’espace de travail. Vous devez utiliser l’authentification SQL.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API d’écriture

```scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Utilisation du connecteur PySpark

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

## <a name="allowing-other-users-to-use-the-dw-connector-in-your-workspace"></a>Utilisation par d’autres utilisateurs du connecteur DW dans l’espace de travail

Vous devez être propriétaire des données blob du Stockage sur le compte de stockage ADLS Gen2 connecté à l’espace de travail pour modifier les autorisations manquantes d’autres personnes. Veillez à ce que l’utilisateur ait accès à l’espace de travail et dispose de l’autorisation d’exécuter des notebooks.

### <a name="option-1"></a>Option 1 :

- Rendre l’utilisateur Propriétaire/Contributeur aux données Blob du stockage

### <a name="option-2"></a>Option 2 :

- Spécifiez les listes ACL suivantes sur la structure de dossiers :

| Dossier | / | synapse | workspaces  | <workspacename> | sparkpools | <sparkpoolname>  | sparkpoolinstances  |
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
