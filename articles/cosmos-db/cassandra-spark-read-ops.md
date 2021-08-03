---
title: Lecture des données de tables de l’API Cassandra à l’aide de Spark
titleSufix: Azure Cosmos DB
description: Cet article explique comment lire les données stockées des tables de l’API Cassandra dans Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: 00e96bc37f8fe613bc5efdf85b1b6721ea1fd77a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464826"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Lire les données de tables de l’API Cassandra dans Azure Cosmos DB à l’aide de Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 Cet article explique comment lire les données stockées dans l’API Cassandra Azure Cosmos DB à partir de Spark.

## <a name="cassandra-api-configuration"></a>Configuration de l’API Cassandra
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
// if using Spark 2.x
// spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

> [!NOTE]
> Si vous utilisez Spark 3.0 ou une version ultérieure, il n’est pas nécessaire d’installer l’assistance Cosmos DB ni la fabrique de connexion. Vous devez également utiliser `remoteConnectionsPerExecutor` à la place de `connections_per_executor_max` pour le connecteur Spark 3 (voir ci-dessus). Vous verrez que les propriétés liées à la connexion sont définies dans le notebook ci-dessus. À l’aide de la syntaxe ci-dessous, les propriétés de connexion peuvent être définies de cette manière, sans qu’il soit nécessaire de les définir au niveau du cluster (initialisation du contexte Spark).

## <a name="dataframe-api"></a>API Dataframe

### <a name="read-table-using-sessionreadformat-command"></a>Lire une table avec la commande session.read.format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Lire une table avec la commande spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Lire des colonnes spécifiques d’une table

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Appliquer des filtres

Vous pouvez effectuer un pushdown de prédicats vers la base de données pour optimiser encore les requêtes Spark. Un prédicat est une condition sur une requête, généralement dans la clause WHERE, qui retourne true ou false. Un pushdown de prédicat filtre les données dans la requête de base de données, réduisant ainsi le nombre d’entrées récupérées à partir de la base de données et améliorant les performances des requêtes. Par défaut, l’API Spark DataSet effectue automatiquement un pushdown des clauses WHERE valides vers la base de données. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

La section `Cassandra Filters` du plan physique comprend le filtre pushed down. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates-spark-3.png" alt-text="partitions":::

## <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient

### <a name="read-table"></a>Lire une table
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Lire des colonnes spécifiques d’une table

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Vues SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Créer une vue temporaire à partir d’un dataframe

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Exécuter des requêtes sur la vue

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Étapes suivantes

Voici des articles supplémentaires sur l’utilisation de l’API Cassandra Azure Cosmos DB à partir de Spark :
 
 * [Opérations d’upsert](cassandra-spark-upsert-ops.md)
 * [Opérations de suppression](cassandra-spark-delete-ops.md)
 * [Opérations d’agrégation](cassandra-spark-aggregation-ops.md)
 * [Opérations de copie de table](cassandra-spark-table-copy-ops.md)

