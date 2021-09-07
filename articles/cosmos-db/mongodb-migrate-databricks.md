---
title: Migrer de MongoDB vers l’API Azure Cosmos DB pour MongoDB à l’aide de Databricks et Spark
description: Découvrez comment utiliser Databricks Spark pour migrer de grands jeux de données à partir d’instances MongoDB vers Azure Cosmos DB.
author: nayakshweta
ms.author: shwetn
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: c49bbef98930fcf9a8312215c6f45b6b04203b2a
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364047"
---
# <a name="migrate-data-from-mongodb-to-an-azure-cosmos-db-api-for-mongodb-account-by-using-azure-databricks"></a>Migrer des données de MongoDB vers une API Azure Cosmos DB pour un compte MongoDB en utilisant Azure Databricks
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ce guide de migration fait partie de la série sur la migration de bases de données de MongoDB vers l’API Azure CosmosDB pour MongoDB. Les principales étapes de migration sont la [prémigration](mongodb-pre-migration.md), la migration proprement dite et la [postmigration](mongodb-post-migration.md), comme indiqué ci-dessous.

:::image type="content" source="./media/mongodb-pre-migration/overall-migration-steps.png" alt-text="Schéma des étapes de migration":::.


## <a name="data-migration-using-azure-databricks"></a>Migration de données à l’aide d’Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) est une offre PaaS (platform as a service) pour [Apache Spark](https://spark.apache.org/). Elle permet d’effectuer des migrations hors connexion sur un grand jeu de données. Vous pouvez utiliser Azure Databricks pour effectuer une migration hors connexion de bases de données de MongoDB vers l’API Azure Cosmos DB pour MongoDB.

Dans ce didacticiel, vous apprendrez à :

- Provisionner un cluster Azure Databricks

- Ajout de dépendances

- Créer et exécuter un notebook Scala ou Python

- Optimiser les performances de migration

- Résoudre les erreurs de limitation du débit qui peuvent être observées durant la migration

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- [Suivez les étapes de prémigration](mongodb-pre-migration.md) comme l’estimation du débit et le choix d’une clé de partition.
- [Créez un compte d’API Azure Cosmos DB pour MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).

## <a name="provision-an-azure-databricks-cluster"></a>Provisionner un cluster Azure Databricks

Vous pouvez suivre les instructions pour [approvisionner un cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Nous vous recommandons de sélectionner le runtime Databricks version 7.6, qui prend en charge Spark 3.0.

:::image type="content" source="./media/mongodb-migrate-databricks/databricks-cluster-creation.png" alt-text="Illustration de la création d’un cluster dans Databricks.":::


## <a name="add-dependencies"></a>Ajout de dépendances

Ajoutez le connecteur MongoDB pour la bibliothèque Spark à votre cluster pour le connecter à MongoDB en mode natif et à l’API Azure Cosmos DB pour points de terminaison MongoDB. Dans votre cluster, sélectionnez **Bibliothèques** > **Installer nouveau** > **Maven**, puis ajoutez les coordonnées Maven `org.mongodb.spark:mongo-spark-connector_2.12:3.0.1`.

:::image type="content" source="./media/mongodb-migrate-databricks/databricks-cluster-dependencies.png" alt-text="Illustration de l’ajout de dépendances de cluster Databricks.":::


Sélectionnez **Installer**, puis redémarrez le cluster une fois l’installation terminée.

> [!NOTE]
> Veillez à redémarrer le cluster Databricks après l’installation du connecteur MongoDB et de la bibliothèque Spark.

Après cela, vous pouvez créer un notebook Scala ou python pour la migration.


## <a name="create-scala-notebook-for-migration"></a>Créer un notebook Scala pour la migration

Créez un notebook Scala dans Databricks. Veillez à entrer les valeurs appropriées pour les variables avant d’exécuter le code suivant :


```scala
import com.mongodb.spark._
import com.mongodb.spark.config._
import org.apache.spark._
import org.apache.spark.sql._

var sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
var sourceDb = "<DBNAME>"
var sourceCollection =  "<COLLECTIONNAME>"
var targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
var targetDb = "<DBNAME>"
var targetCollection =  "<COLLECTIONNAME>"

val readConfig = ReadConfig(Map(
  "spark.mongodb.input.uri" -> sourceConnectionString,
  "spark.mongodb.input.database" -> sourceDb,
  "spark.mongodb.input.collection" -> sourceCollection,
))

val writeConfig = WriteConfig(Map(
  "spark.mongodb.output.uri" -> targetConnectionString,
  "spark.mongodb.output.database" -> targetDb,
  "spark.mongodb.output.collection" -> targetCollection,
  "spark.mongodb.output.maxBatchSize" -> "8000"  
))

val sparkSession = SparkSession
  .builder()
  .appName("Data transfer using spark")
  .getOrCreate()

val customRdd = MongoSpark.load(sparkSession, readConfig)

MongoSpark.save(customRdd, writeConfig)
```

## <a name="create-python-notebook-for-migration"></a>Créer un notebook Python pour la migration

Créez un notebook Python dans Databricks. Veillez à entrer les valeurs appropriées pour les variables avant d’exécuter le code suivant :


```python
from pyspark.sql import SparkSession

sourceConnectionString = "mongodb://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<AUTHDB>"
sourceDb = "<DBNAME>"
sourceCollection =  "<COLLECTIONNAME>"
targetConnectionString = "mongodb://<ACCOUNTNAME>:<PASSWORD>@<ACCOUNTNAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<ACCOUNTNAME>@"
targetDb = "<DBNAME>"
targetCollection =  "<COLLECTIONNAME>"

my_spark = SparkSession \
    .builder \
    .appName("myApp") \
    .getOrCreate()

df = my_spark.read.format("com.mongodb.spark.sql.DefaultSource").option("uri", sourceConnectionString).option("database", sourceDb).option("collection", sourceCollection).load()

df.write.format("mongo").mode("append").option("uri", targetConnectionString).option("maxBatchSize",2500).option("database", targetDb).option("collection", targetCollection).save()
```

## <a name="optimize-the-migration-performance"></a>Optimiser les performances de migration

Les performances de la migration peuvent être ajustées à l’aide de ces configurations :

- **Nombre de Workers et de cœurs dans le cluster Spark** : plus le nombre de rôles de travail est élevé, plus vous disposerez de nœuds de calcul pour l’exécution des tâches.

- **maxBatchSize** : la valeur `maxBatchSize` contrôle la vitesse à laquelle les données sont enregistrées dans la collection Azure Cosmos DB cible. Toutefois, si la valeur maxBatchSize est trop élevée pour le débit de collection, cela peut entraîner des erreurs de [limitation du débit](prevent-rate-limiting-errors.md).

  Vous devez ajuster le nombre de Workers et la valeur maxBatchSize en fonction du nombre d’Exécuteurs dans le cluster Spark, éventuellement de la taille (et donc du coût de RU) de chaque document et des limites de débit de la collection cible.

  >[!TIP]
  >maxBatchSize = Débit de collection / (coût de RU pour 1 document \* nombre de workers Spark \* nombre de cœurs de processeur par Worker)

- **Partitionneur MongoDB Spark et clé de partition** : le partitionneur par défaut utilisé est MongoDefaultPartitioner et la clé de partition par défaut est _id. Vous pouvez modifier le partitionneur en affectant la valeur `MongoSamplePartitioner` à la propriété de configuration d’entrée `spark.mongodb.input.partitioner`. De même, vous pouvez modifier la clé de partition en affectant le nom de champ approprié à la propriété de configuration d’entrée `spark.mongodb.input.partitioner.partitionKey`. La clé de partition appropriée permet d’éviter l’asymétrie des données (un grand nombre d’enregistrements écrits pour la même valeur de clé de partition).

- **Désactivez les index durant le transfert de données :** pour la migration de grandes quantités de données, envisagez de désactiver les index, en particulier l’index générique sur la collection cible. Les index augmentent le coût de RU pour l’écriture de chaque document. La libération de ces RU peut contribuer à améliorer la vitesse de transfert des données. Vous pouvez activer les index après la migration des données.



## <a name="troubleshoot"></a>Dépanner

### <a name="timeout-error-error-code-50"></a>Erreur de délai d’expiration (code d’erreur 50)
Vous pouvez constater un code d’erreur 50 pour les opérations sur l’API Cosmos DB pour la base de données MongoDB. Les scénarios suivants peuvent entraîner des erreurs de délai d’expiration :

- **Le débit alloué à la base de données est faible** : Vérifiez qu’un débit suffisant est attribué à la collection cible.
- **Asymétrie excessive des données avec un volume important de données**. Si vous avez une grande quantité de données à migrer vers une table donnée, mais que les données présentent une asymétrie importante, vous pouvez quand même subir une limitation de débit, même si vous avez provisionné plusieurs [unités de requête](request-units.md) dans votre table. Les unités de requête sont réparties de manière égale entre les partitions physiques, et une forte asymétrie des données peut provoquer un goulot d’étranglement des requêtes adressées à une seule partition. L’asymétrie des données signifie qu’il existe un grand nombre d’enregistrements pour la même valeur de clé de partition.

### <a name="rate-limiting-error-code-16500"></a>Limitation du débit (code d’erreur 16500)

Vous pouvez constater un code d’erreur 16500 pour les opérations sur l’API Cosmos DB pour la base de données MongoDB. Il s’agit d’erreurs de limitation du débit qui peuvent être observées sur des comptes anciens ou des comptes où la fonctionnalité de nouvelle tentative côté serveur est désactivée.
- **Activer la fonctionnalité de nouvelle tentative côté serveur** : Activez la fonctionnalité de nouvelle tentative côté serveur et laissez le serveur réessayer automatiquement les opérations à débit limité.



## <a name="post-migration-optimization"></a>Optimisation de la post-migration

Après avoir migré les données, vous pouvez vous connecter à Azure Cosmos DB et gérer les données. Vous pouvez également effectuer d’autres étapes post-migration comme l’optimisation de la stratégie d’indexation, la mise à jour du niveau de cohérence par défaut ou la configuration de la distribution mondiale pour votre compte Azure Cosmos DB. Pour plus d’informations, consultez l’article [Optimisation de la post-migration](mongodb-post-migration.md).

## <a name="next-steps"></a>Étapes suivantes

* [Gérer l’indexation dans l’API pour MongoDB d’Azure Cosmos DB](mongodb-indexing.md)

* [Rechercher les frais des unités de requête pour les opérations](find-request-unit-charge-mongodb.md)
