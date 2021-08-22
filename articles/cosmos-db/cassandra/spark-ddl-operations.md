---
title: Opérations DDL dans l’API Cassandra Azure Cosmos DB à partir de Spark
description: Cet article décrit en détail les opérations DDL d’espace de clés et de table dans l’API Cassandra Azure Cosmos DB à partir de Spark.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 99eee3fd7506f3baf8b119b3fdf4ba881850d61e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562227"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Opérations DDL dans l’API Cassandra Azure Cosmos DB à partir de Spark
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Cet article décrit en détail les opérations DDL d’espace de clés et de table dans l’API Cassandra Azure Cosmos DB à partir de Spark.

## <a name="spark-context"></a>Contexte Spark

 Le connecteur pour l’API Cassandra nécessite que les informations de connexion Cassandra soient initialisées dans le cadre du contexte Spark. Quand vous lancez un notebook, le contexte Spark est déjà initialisé. Il n’est pas recommandé de l’arrêter et de le réinitialiser. Une solution consiste à ajouter la configuration de l’instance de l’API Cassandra à un niveau de cluster, dans la configuration du cluster spark. Cette opération s’effectue une seule fois par cluster. Ajoutez à la configuration Spark le code suivant, où chaque paire clé-valeur utilise un espace en guise de séparation :
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="cassandra-api-related-configuration"></a>Configuration liée à l’API Cassandra 

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra
//spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

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
> Si vous utilisez la version 3.0 ou une version ultérieure de Spark, il n’est pas nécessaire d’installer l’assistance Cosmos DB ni la fabrique de connexion. Par ailleurs, utilisez `remoteConnectionsPerExecutor` plutôt que `connections_per_executor_max` pour le connecteur Spark 3 (cf. ci-dessus).

## <a name="keyspace-ddl-operations"></a>Opérations DDL d’espace de clés

### <a name="create-a-keyspace"></a>Créer un espace de clés

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Valider dans cqlsh

Exécutez la commande suivante dans cqlsh ; vous devriez voir l’espace de clés créé précédemment.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Supprimer un espace de clés

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Valider dans cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Opérations DDL de table

**Considérations :**  

- Le débit peut être assigné au niveau de la table à l’aide de l’instruction create table.  
- Une clé de partition peut stocker 20 Go de données.  
- Un enregistrement peut stocker un maximum de 2 Mo de données.  
- Une plage de clés de partition peut stocker plusieurs clés de partition.

### <a name="create-a-table"></a>Créer une table

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Valider dans cqlsh

Exécutez la commande suivante dans cqlsh ; vous devriez voir la table nommée «books» : 

```bash
USE books_ks;
DESCRIBE books;
```

Les valeurs de durée de vie par défaut et de débit provisionnées ne figurent pas dans la sortie de la commande précédente ; vous pouvez les obtenir à partir du portail.

### <a name="alter-table"></a>Modifier une table

Vous pouvez modifier les valeurs suivantes à l’aide de la commande alter table :

* Débit provisionné 
* Durée de vie
<br>Les modifications de colonne ne sont pas prises en charge.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Supprimer une table

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Valider dans cqlsh

Exécutez la commande suivante dans cqlsh ; vous devriez voir que la table « books » n’est plus disponible :

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé l’espace de clés et la table, passez aux articles suivants, qui traitent, entre autres, des opérations CRUD :
 
* [Opérations de création et d’insertion](spark-create-operations.md)  
* [Lire les opérations](spark-read-operation.md)  
* [Opérations d’upsert](spark-upsert-operations.md)  
* [Opérations de suppression](spark-delete-operation.md)  
* [Opérations d’agrégation](spark-aggregation-operations.md)  
* [Opérations de copie de table](spark-table-copy-operations.md)  
