---
title: Opérations d’upsert de données dans l’API Cassandra Azure Cosmos DB à partir de Spark
description: Cet article explique comment effectuer un upsert dans des tables au sein de l’API Cassandra Azure Cosmos DB à partir de Spark
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e4858ca9532c65cd1a4a0a3c8cffea2f8b8463cd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524689"
---
# <a name="upsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Opérations d’upsert de données dans l’API Cassandra Azure Cosmos DB à partir de Spark
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Cet article explique comment effectuer un upsert de données dans l’API Cassandra Azure Cosmos DB à partir de Spark.

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
> Si vous utilisez Spark 3.0 ou une version ultérieure, il n’est pas nécessaire d’installer l’assistance Cosmos DB ni la fabrique de connexion. Vous devez également utiliser `remoteConnectionsPerExecutor` à la place de `connections_per_executor_max` pour le connecteur Spark 3 (voir ci-dessus). Vous verrez que les propriétés liées à la connexion sont définies dans le notebook ci-dessus. À l’aide de la syntaxe ci-dessous, les propriétés de connexion peuvent être définies de cette manière, sans qu’il soit nécessaire de les définir au niveau du cluster (initialisation du contexte Spark). Toutefois, quand vous utilisez des opérations qui requièrent un contexte Spark (par exemple `CassandraConnector(sc)` pour `update` comme indiqué ci-dessous), les propriétés de connexion doivent être définies au niveau du cluster.

## <a name="dataframe-api"></a>API Dataframe

### <a name="create-a-dataframe"></a>Créer un dataframe 

```scala
// (1) Update: Changing author name to include prefix of "Sir"
// (2) Insert: adding a new book

val booksUpsertDF = Seq(
    ("b00001", "Sir Arthur Conan Doyle", "A study in scarlet", 1887),
    ("b00023", "Sir Arthur Conan Doyle", "A sign of four", 1890),
    ("b01001", "Sir Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
    ("b00501", "Sir Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
    ("b00300", "Sir Arthur Conan Doyle", "The hounds of Baskerville", 1901),
    ("b09999", "Sir Arthur Conan Doyle", "The return of Sherlock Holmes", 1905)
    ).toDF("book_id", "book_author", "book_name", "book_pub_year")
booksUpsertDF.show()
```

### <a name="upsert-data"></a>Effectuer un upsert de données

```scala
// Upsert is no different from create
booksUpsertDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .save()
```

### <a name="update-data"></a>Mettre à jour des données

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

//This runs on the driver, leverage only for one off updates
cdbConnector.withSessionDo(session => session.execute("update books_ks.books set book_price=99.33 where book_id ='b00300' and book_pub_year = 1901;"))
```

## <a name="rdd-api"></a>API pour le jeu de donnée distribué résilient
> [!NOTE]
> L’upsert à partir de l’API RDD est identique à l’opération de création 

## <a name="next-steps"></a>Étapes suivantes

Passez aux articles suivants pour effectuer d’autres opérations sur les données stockées dans des tables de l’API Cassandra Azure Cosmos DB :
 
* [Opérations de suppression](spark-delete-operation.md)
* [Opérations d’agrégation](spark-aggregation-operations.md)
* [Opérations de copie de table](spark-table-copy-operations.md)
