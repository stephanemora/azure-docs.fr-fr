---
title: API Hive Warehouse Connector dans Azure HDInsight
description: Découvrez les différentes API de Hive Warehouse Connector.
author: adesh-rao
ms.author: adrao
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/29/2021
ms.openlocfilehash: 4816830feac9aceb34ecfaa701ea0b7aec9c728b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532586"
---
# <a name="hive-warehouse-connector-apis-in-azure-hdinsight"></a>API Hive Warehouse Connector dans Azure HDInsight

Cet article liste toutes les API prises en charge par Hive Warehouse Connector. Tous les exemples présentés ci-dessous sont exécutés à l’aide de l’interpréteur de commandes Spark et d’une session Hive Warehouse Connector.

Comment créer une session Hive Warehouse Connector :

```scala
import com.hortonworks.hwc.HiveWarehouseSession
val hive = HiveWarehouseSession.session(spark).build()
```

## <a name="prerequisite"></a>Configuration requise

Suivez les étapes de [Configuration de Hive Warehouse Connector](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup).


## <a name="supported-apis"></a>API prises en charge

- Définir la base de données :
    ```scala
    hive.setDatabase("<database-name>")
    ```

- Lister toutes les bases de données :
    ```scala
    hive.showDatabases()
    ```

- Lister toutes les tables de la base de données active
    ```scala
    hive.showTables()
    ```

- Décrire une table
    
    ```scala
   // Describes the table <table-name> in the current database
    hive.describeTable("<table-name>")
    ```
    
    ```scala
   // Describes the table <table-name> in <database-name>
    hive.describeTable("<database-name>.<table-name>")
    ```

- Déposer une base de données
    
    ```scala
   // ifExists and cascade are boolean variables
    hive.dropDatabase("<database-name>", ifExists, cascade)
    ```

- Supprimer une table de la base de données active
    
    ```scala
    // ifExists and purge are boolean variables
    hive.dropTable("<table-name>", ifExists, purge)
    ```

- Création d'une base de données
    ```scala
   // ifNotExists is boolean variable
    hive.createDatabase("<database-name>", ifNotExists)
    ```

- Créer une table dans la base de données active
    ```scala
   // Returns a builder to create table
    val createTableBuilder = hive.createTable("<table-name>")
    ```
    
    Le générateur pour la création de table prend en charge uniquement les opérations ci-dessous : 
    
    ```scala
   // Create only if table does not exists already
    createTableBuilder = createTableBuilder.ifNotExists()
    ```
    
    ```scala
   // Add columns
    createTableBuilder = createTableBuilder.column("<column-name>", "<datatype>")
    ```
    
    ```scala
    // Add partition column
    createTableBuilder = createTableBuilder.partition("<partition-column-name>", "<datatype>")
    ```
    ```scala
   // Add table properties
    createTableBuilder = createTableBuilder.prop("<key>", "<value>")
    ```
    ```scala
    // Creates a bucketed table,
    // Parameters are numOfBuckets (integer) followed by column names for bucketing
    createTableBuilder = createTableBuilder.clusterBy(numOfBuckets, "<column1>", .... , "<columnN>")
    ```
    
    ```scala
    // Creates the table
    createTableBuilder.create()
    ```

    > [!NOTE]
    > Cette API crée une table au format ORC à l’emplacement par défaut. Pour d’autres fonctionnalités/options ou pour créer une table à l’aide de requêtes Hive, utilisez l’API `executeUpdate`.


- Lire une table

    ```scala
   // Returns a Dataset<Row> that contains data of <table-name> in the current database
    hive.table("<table-name>")
    ```

- Exécuter des commandes DDL sur HiveServer2 

    ```scala
    // Executes the <hive-query> against HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>")
    ```
    
    ```scala
    // Executes the <hive-query> against HiveServer2
    // Throws exception, if propagateException is true and query threw excpetion in HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>", propagateException) // propagate exception is boolean value
    ```

- Exécuter une requête Hive et charger le résultat dans un jeu de données
    
  - Exécution d’une requête par le biais de démons LLAP. **[Recommandé]**
    ```scala
    // <hive-query> should be a hive query 
    hive.executeQuery("<hive-query>")
    ```
  - Exécution d’une requête par le biais de HiveServer2 via JDBC.

    Définir `spark.datasource.hive.warehouse.smartExecution` sur `false` dans les configurations Spark avant de démarrer la session Spark pour utiliser cette API
    ```scala
    hive.execute("<hive-query>")
    ```

- Fermer une session Hive Warehouse Connector
    ```scala
    // Closes all the open connections and
    // release resources/locks from HiveServer2
    hive.close()
    ```

- Exécuter une requête de fusion Hive
    
    Cette API crée une requête de fusion Hive au format ci-dessous
    
    ```
    MERGE INTO <current-db>.<target-table> AS <targetAlias> USING <source expression/table> AS <sourceAlias>
    ON <onExpr>
    WHEN MATCHED [AND <updateExpr>] THEN UPDATE SET <nameValuePair1> ... <nameValuePairN>
    WHEN MATCHED [AND <deleteExpr>] THEN DELETE
    WHEN NOT MATCHED [AND <insertExpr>] THEN INSERT VALUES <value1> ... <valueN>
    ```

    ```scala
    val mergeBuilder = hive.mergeBuilder() // Returns a builder for merge query
    ```
    Le générateur prend en charge les opérations suivantes :
    
    ```scala
    mergeBuilder.mergeInto("<taget-table>", "<targetAlias>")
    ```
    
    ```scala
    mergeBuilder.using("<source-expression/table>", "<sourceAlias>")
    ```
    
    ```scala
    mergeBuilder.on("<onExpr>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenUpdate("<updateExpr>", "<nameValuePair1>", ... , "<nameValuePairN>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenDelete("<deleteExpr>")
    ```
    
    ```scala
    mergeBuilder.whenNotMatchedInsert("<insertExpr>", "<value1>", ... , "<valueN>");
    ```

    ```scala
    // Executes the merge query
    mergeBuilder.merge()
    ```

- Écrire un jeu de données dans la table Hive par lot
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector")
       .option("table", tableName)
       .mode(SaveMode.Type)
       .save()
    ```
   - TableName être au format `<db>.<table>` ou `<table>`. Si aucun nom de base de données n’est fourni, la table est explorée/créée dans la base de données active
    
   - Les types SaveMode (Mode d’enregistrement) sont les suivants :
    
     - Append : ajoute le jeu de données à la table spécifiée
    
     - Overwrite : remplace les données de la table spécifiée par le jeu de données
    
     - Ignore : ignore l’écriture si la table existe déjà ; aucune erreur n’est générée
    
     - ErrorIfExists : génère une erreur si la table existe déjà


- Écrire un jeu de données dans une table Hive à l’aide de HiveStreaming
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("metastoreUri", "<HMS_URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    
     // To write to static partition
     df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("partition", partition)
       .option("metastoreUri", "<HMS URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    ```
    > [!NOTE]
    > Les écritures en flux ajoutent toujours des données.


- Écriture d’un flux Spark dans une table Hive
    ```scala
    stream.writeStream
        .format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource")
        .option("metastoreUri", "<HMS_URI>")
        .option("database", databaseName)
        .option("table", tableName)
      //.option("partition", partition) , add if inserting data in partition
      //.option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
        .start()
    ```
