---
title: Connecter Apache Spark à Azure Cosmos DB
description: Découvrez le connecteur Spark Azure Cosmos DB qui vous permet de connecter Apache Spark à Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bc0f2044f70c674177f9c9786f56f0441db2e282
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978908"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Accélérer l’analytique en temps réel des Big Data au moyen du connecteur Apache Spark-Azure Cosmos DB

Vous pouvez exécuter [Spark](https://spark.apache.org/) travaux avec les données stockées dans Azure Cosmos DB à l’aide du connecteur Spark Cosmos DB. COSMOS peut être utilisé pour batch et de traitement de flux et comme une couche de service pour l’accès à faible latence.

Vous pouvez utiliser le connecteur avec [Azure Databricks](https://azure.microsoft.com/services/databricks) ou [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), qui fournissent des clusters Spark gérés sur Azure. Le tableau suivant présente des versions prises en charge de Spark.

| Composant | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x et 2.1.x |
| Scala | 2.11 |
| Version du runtime Azure Databricks | > 3.4 |

> [!WARNING]
> Ce connecteur prend en charge l’API de base (SQL) d’Azure Cosmos DB.
> Pour Cosmos DB pour l’API MongoDB, utilisez le [connecteur MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Pour l’API de Cassandra Cosmos DB, utilisez le [connecteur Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Démarrage rapide

* Suivez les étapes de [prise en main du Kit de développement logiciel Java](sql-api-async-java-get-started.md) pour configurer un compte Cosmos DB et insérez des données.
* Suivez les étapes de [mise en route de Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) pour configurer un espace de travail Azure Databricks et d’un cluster.
* Vous pouvez désormais créer de nouveaux ordinateurs portables et importer la bibliothèque de connecteur Cosmos DB. Accéder à [fonctionne avec le connecteur Cosmos DB](#bk_working_with_connector) pour plus d’informations sur la façon de configurer votre espace de travail.
* La section suivante a des extraits de code sur la façon de lire et écrire à l’aide du connecteur.

### <a name="reading-from-cosmos-db"></a>Lecture à partir de Cosmos DB

L’extrait de code suivant montre comment créer une trame de données Spark pour lire à partir de Cosmos DB dans PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Et l’extrait de code dans Scala :

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="writing-to-cosmos-db"></a>Écriture dans Cosmos DB

L’extrait de code suivant montre comment écrire une trame de données dans Cosmos DB dans PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

Et l’extrait de code dans Scala :

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Plus d’extraits de code et exemples de bout en bout, consultez [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Utilisation du connecteur

Vous pouvez créer le connecteur à partir de la source dans Github, ou télécharger les fichiers d’uber JAR à partir de Maven dans les liens ci-dessous.

| Spark | Scala | Version la plus récente |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>À l’aide de blocs-notes Databricks

Créer une bibliothèque à l’aide de votre espace de travail Databricks en suivant les instructions dans le Guide de Databricks Azure > [utiliser le connecteur Spark Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Notez que le **utiliser le connecteur Spark Azure Cosmos DB** page n’est actuellement pas à jour. Au lieu de télécharger les fichiers JAR de distincts six dans six différentes bibliothèques, vous pouvez télécharger le fichier uber jar à partir de maven au https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) et installer cette bibliothèque/jar un.
> 

### <a name="using-spark-cli"></a>À l’aide de spark-cli

Pour travailler avec le connecteur à l’aide de la cli de spark (autrement dit, `spark-shell`, `pyspark`, `spark-submit`), vous pouvez utiliser la `--packages` paramètre avec le connecteur [coordonnées maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>À l’aide de blocs-notes Jupyter

Si vous utilisez des blocs-notes Jupyter dans HDInsight, vous pouvez utiliser la commande magique spark `%%configure` cellule pour spécifier les coordonnées maven du connecteur.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Remarque : l’inclusion de le `spark.jars.excludes` est spécifique à supprimer les conflits potentiels entre le connecteur, Apache Spark et Livy.

### <a name="build-the-connector"></a>Créer le connecteur

Actuellement, ce projet de connecteur utilise `maven` pour générer sans dépendances, vous pouvez donc exécuter :

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Utilisation de nos exemples

Le [Cosmos DB Spark référentiel](https://github.com/Azure/azure-cosmosdb-spark) a les exemples de bloc-notes suivant et les scripts que vous pouvez essayer.

* **Vol de ponctualité avec Spark et Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Connecter Spark à Cosmos DB à l’aide du service de bloc-notes Jupyter de HDInsight pour tirer parti de Spark SQL, GraphFrames et prédire les retards de vol à l’aide de pipelines ML.
* **[Connexion de Spark Cosmos DB de flux de modification](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Une vitrine rapide sur la façon de connecter Spark à Cosmos DB Change Feed.
* **Twitter Source avec Apache Spark et Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **L’utilisation d’Apache Spark pour interroger Cosmos DB graphiques**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Connecter Azure Databricks à Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  à l’aide de `azure-cosmosdb-spark`.  Lié ici est également une version d’Azure Databricks de la [bloc-notes de vol de ponctualité](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architecture lambda avec Azure Cosmos DB et HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Vous pouvez réduire les frais opérationnels de gestion des pipelines big data à l’aide de Cosmos DB et Spark.

## <a name="more-information"></a>Informations complémentaires

Nous avons plus d’informations le `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) , y compris :

* [Guide de l’utilisateur connecteur Spark Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exemples d’agrégations](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuration et installation

* [Configuration du connecteur Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark à Cosmos DB Connector Setup](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (en cours)
* [Configuration de Power BI Direct Query à Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Résolution de problèmes

* [À l’aide de Cosmos DB agrégats](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problèmes connus](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Performances

* [Conseils sur les performances](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Séries de tests de requête](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Séries de tests d’écriture](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Modifier le flux

* [Modifications de traitement Stream à l’aide d’Azure Cosmos DB Change Feed et Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Démonstrations de flux de modification](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Démonstrations de Structured Stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Surveillance

* [Surveillance des travaux Spark avec application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Étapes suivantes

Si cela n’est pas déjà fait, téléchargez le connecteur Spark-Azure Cosmos DB à partir du dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Explorez les ressources supplémentaires suivantes dans le dépôt :

* [Aggregations Examples](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples) (Exemples d’agrégations)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Exemples de scripts et Notebooks)

Vous pouvez également consulter [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Guide sur Apache Spark SQL, les tableaux de données et les jeux de données) et l’article [Apache Spark sur Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
