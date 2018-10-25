---
title: Connecter Apache Spark à Azure Cosmos DB | Microsoft Docs
description: Découvrez le connecteur Spark Azure Cosmos DB qui vous permet de connecter Apache Spark à Azure Cosmos DB. Vous pouvez effectuer des agrégations distribuées sur le système de base de données multilocataire, distribué dans le monde entier à partir de Microsoft.
keywords: apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: 26479ade27b18e817d7ad03d70d27b0cef687e8d
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269409"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Accélérer l’analytique en temps réel des Big Data au moyen du connecteur Apache Spark-Azure Cosmos DB
 
Le connecteur Apache Spark-Azure Cosmos DB permet à Azure Cosmos DB d’être une entrée ou une sortie pour les tâches Apache Spark. Le fait de connecter [Spark](http://spark.apache.org/) à [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) accélère votre capacité à résoudre les problèmes liés aux opérations de sciences de données dynamiques. Vous pouvez utiliser Azure Cosmos DB pour conserver et interroger rapidement des données. Le connecteur utilise efficacement les index gérés natifs d’Azure Cosmos DB. Les index activent des colonnes modifiables quand vous effectuez des analyses et opérez un filtrage de prédicat sur des données globalement distribuées qui changent rapidement. Ce genre de données va de l’Internet des objets (IoT) aux scénarios de sciences et d’analyse des données.

## <a name="connector-components"></a>Composants du connecteur

Le connecteur Spark-Azure Cosmos DB comprend les composants suivants :

* [Azure Cosmos DB](http://documentdb.com) vous permet de provisionner et d’adapter de façon élastique le débit et le stockage dans n’importe quel nombre de régions géographiques.  

* [Apache Spark](http://spark.apache.org/) est un moteur de traitement open source puissant qui combine vitesse, facilité d’utilisation et analytique sophistiquée.  

* Le [cluster Apache Spark sur Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) vous permet d’exécuter des travaux Spark sur le cluster Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Connecter Apache Spark à Azure Cosmos DB

Vous pouvez choisir entre deux approches pour connecter Apache Spark et Azure Cosmos DB :

- [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), un connecteur basé sur Python (aussi appelé *pyDocumentDB*).  

- [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java), un connecteur basé sur Java.


**Versions prises en charge**

| Composant | Version |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Version du runtime Azure Databricks | > 3.4 |
| Kit SDK Java SQL Azure Cosmos DB | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Se connecter à l’aide de Python ou de pyDocumentDB SDK

Le diagramme suivant illustre l’architecture de l’implémentation du SDK pyDocumentDB :

![Diagramme du flux de données Spark-Azure Cosmos DB par le biais de pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Flux de données

Le flux de données de l’implémentation de pyDocumentDB se présente de la façon suivante :

* Le nœud principal de Spark se connecte au nœud de passerelle Azure Cosmos DB par le biais de pyDocumentDB. Vous spécifiez uniquement les connexions Spark et Azure Cosmos DB. Les connexions aux nœuds principal et de passerelle sont transparentes.  

* Le nœud de passerelle effectue la requête sur Azure Cosmos DB, sachant que la requête s’exécute par la suite sur les partitions de la collection dans les nœuds de données. La réponse à ces requêtes est renvoyée au nœud de passerelle, puis le jeu de résultats est retourné au nœud principal Spark.  

* Toutes les requêtes suivantes (par exemple, sur une trame de données Spark) sont envoyées aux nœuds de travail Spark à des fins de traitement.  

La communication entre Spark et Azure Cosmos DB est limitée au nœud principal Spark et aux nœuds de passerelle Azure Cosmos DB. Les requêtes sont opérées aussi rapidement que la couche de transport entre ces deux nœuds le permet.

Exécutez les étapes suivantes pour connecter Spark à Azure Cosmos DB à l’aide du SDK pyDocumentDB :

1. Créez un [espace de travail Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) et un [cluster Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Le runtime Azure Databricks version 4.0 inclut Apache Spark 2.3.0 et Scala 2.11 au sein de cet espace de travail.  

2. Une fois le cluster créé et en cours d’exécution, accédez à **Workspace** (Espace de travail) > **Create** (Créer) > **Library** (Bibliothèque).  
3. Dans la boîte de dialogue New Library (Nouvelle bibliothèque), choisissez **Upload Python Egg or PyPi** (Charger Python Egg ou PyPi) comme source. Indiquez **pydocumentdb** comme nom, puis sélectionnez **Install Library** (Installer la bibliothèque). Le SDK pyDocumentdb étant déjà publié sur les packages pip, vous pouvez le trouver et l’installer. 

   ![Capture d’écran de la boîte de dialogue New Library (Nouvelle bibliothèque)](./media/spark-connector/create-library.png)

4. Lorsque la bibliothèque est installée, attachez-la au cluster que vous avez créé précédemment.  

5. Accédez à **Workspace** (Espace de travail) > **Create** (Créer) > **Notebook**.  

6. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial et choisissez **Python** comme langage. Dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

7. Exécutez quelques requêtes Spark à l’aide des exemples de données de vols hébergés dans le compte Azure Cosmos DB « doctorwho ». (Ce compte est accessible publiquement). Le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) héberge la version HTML du notebook. Téléchargez les fichiers du dépôt et accédez à `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html`. Vous pouvez importer le notebook dans votre compte Azure Databricks et l’exécuter. La section suivante explique de façon détaillée les fonctionnalités des blocs de code.

L’extrait de code suivant montre comment importer le SDK pyDocumentDB et exécuter une requête dans le contexte Spark. Comme indiqué dans l’extrait de code, pyDocumentDB SDK contient les paramètres de connexion nécessaires pour se connecter au compte Azure Cosmos DB. Il importe les bibliothèques demandées et configure la clé principale et l’hôte pour créer le client Azure Cosmos DB (pydocumentdb.document_client).


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

Ensuite, vous pouvez exécuter des requêtes. L’extrait de code suivant se connecte à la collection airports.codes dans le compte doctorwho, puis exécute une requête pour extraire les villes de l’État de Washington disposant d’un aéroport. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

Une fois la requête exécutée, le résultat est un élément « query_iterable.QueryIterable » qui est converti en liste Python. Cette liste, à son tour, est convertie en trame de données Spark. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>Considérations relatives à l’utilisation de pyDocumentDB SDK
La connexion de Spark à Azure Cosmos DB au moyen du SDK pyDocumentDB est recommandée dans les scénarios suivants :

* Vous souhaitez utiliser Python.  

* Un jeu de résultats relativement petit est retourné d’Azure Cosmos DB à Spark. Notez que le jeu de données sous-jacent dans Azure Cosmos DB peut être très volumineux, et que vous appliquez des filtres ou exécutez des filtres de prédicat sur votre source Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Se connecter à l’aide de Java SDK

Le diagramme suivante illustre l’architecture de l’implémentation du SDK Java SQL Azure Cosmos DB et les déplacements de données entre les nœuds de travail Spark :

![Diagramme du flux de données dans le connecteur Spark-Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Flux de données

Le flux de données de l’implémentation de Java SDK se présente de la façon suivante :

* Le nœud principal Spark se connecte au nœud de passerelle Azure Cosmos DB pour obtenir le mappage de partitions. Vous spécifiez uniquement les connexions Spark et Azure Cosmos DB. Les connexions aux nœuds principal et de passerelle sont transparentes.  

* Ces informations sont renvoyées au nœud maître Spark. À ce stade, vous devez être en mesure d’analyser la requête pour déterminer les partitions (et leurs emplacements) auxquelles vous devez accéder dans Azure Cosmos DB.  

* Ces informations sont transmises aux nœuds worker Spark.  

* Les nœuds de travail Spark se connectent directement aux partitions Azure Cosmos DB pour extraire les données et les retourner aux partitions Spark dans les nœuds de travail.  

La communication entre Spark et Azure Cosmos DB est beaucoup plus rapide, car les données sont déplacées entre les nœuds worker Spark et les nœuds de données Azure Cosmos DB (partitions). Dans cet exemple, vous envoyez des exemples de données Twitter au compte Azure Cosmos DB et exécutez des requêtes Spark en utilisant ces données. Utilisez les étapes suivantes pour écrire des exemples de données Twitter dans Azure Cosmos DB :

1. Créez un [compte API SQL Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) et [ajoutez une collection](create-sql-api-dotnet.md#add-a-collection) au compte.  

2. Téléchargez l’exemple [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) à partir de GitHub. Vous utilisez ce flux pour écrire des exemples de données Twitter dans Azure Cosmos DB.  

3. Ouvrez une invite de commandes et installez les modules Tweepy et pyDocumentdb en exécutant les commandes suivantes :

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extrayez le contenu de l’exemple de flux Twitter et ouvrez le fichier config.py. Mettez à jour les valeurs de masterKey, host, databaseId, collectionId et preferredLocations.  

5. Accédez à `http://apps.twitter.com/` et inscrivez l’application de flux Twitter. Après avoir choisi un nom pour votre application, vous recevez les valeurs de **consumer key (clé du consommateur), consumer secret (clé secrète du consommateur), access token (jeton d’accès) et access token secret (clé secrète du jeton d’accès)**. Copiez ces valeurs et mettez-les à jour dans le fichier config.py pour fournir l’accès de l’application par programmation au flux Twitter à Twitter.   

6. Enregistrez le fichier config.py. Ouvrez une invite de commandes et exécutez l’application Python à l’aide de la commande suivante :

   ```bash
   Python driver.py
   ```

7. Accédez à la collection Azure Cosmos DB dans le portail et vérifiez que les données Twitter sont écrites dans la collection.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Rechercher et attacher le SDK Java au cluster Spark

1. Créez un [espace de travail Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) et un [cluster Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Le runtime Azure Databricks version 4.0 inclut Apache Spark 2.3.0 et Scala 2.11 au sein de cet espace de travail.  

2. Une fois le cluster créé et en cours d’exécution, accédez à **Workspace** (Espace de travail) > **Create** (Créer) > **Library** (Bibliothèque).  

3. Dans la boîte de dialogue **New Library** (Nouvelle bibliothèque), choisissez **Maven Coordinate** (Coordonnée Maven) comme source. Fournissez la valeur de coordonnée **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**, puis sélectionnez **Create Library** (Créer la bibliothèque). Les dépendances de Maven sont résolues, et le package est ajouté à votre espace de travail. Dans le format de coordonnée Maven précédent, 2.3.0 représente la version de Spark, 2.11 la version de Scala, et 1.2.0 la version du connecteur Azure Cosmos DB. 

4. Lorsque la bibliothèque est installée, attachez-la au cluster que vous avez créé précédemment. 

Cet article illustre l’utilisation du connecteur Spark-SDK Java dans les scénarios suivants :

* Lire des données Twitter d’Azure Cosmos DB.  

* Lire des données Twitter diffusées en streaming vers Azure Cosmos DB.  

* Écrire des données Twitter dans Azure Cosmos DB. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Lire des données Twitter à partir d’Azure Cosmos DB.
 
Dans cette section, vous exécutez des requêtes Spark pour lire un lot de données Twitter à partir d’Azure Cosmos DB. Le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) héberge la version HTML du notebook. Téléchargez les fichiers du dépôt et accédez à `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html`. Vous pouvez importer le notebook dans votre compte Azure Databricks et mettre à jour l’URI du compte, la clé principale, la base de données et les noms de collection. Vous pouvez exécuter le notebook ou le créer comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace** (Espace de travail) > **Create** (Créer) > **Notebook**. 

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial et choisissez **Python** comme langage. Dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

3. Mettez à jour les valeurs du point de terminaison, de la clé principale, de la base de données et de la collection pour vous connecter au compte. Lisez les tweets à l’aide de la commande spark.read.format().

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Exécutez la requête pour obtenir le nombre de tweets par hashtags différents à partir des données mises en cache. 

   ```python
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

Java SDK prend en charge les valeurs suivantes pour le mappage de configuration : 

|Paramètre  |Description  |
|---------|---------|
|query_maxdegreeofparallelism  | Définit le nombre d’opérations simultanées qui sont exécutées côté client lors de l’exécution de la requête en parallèle. S’il est défini sur une valeur qui est supérieure à 0, il limite le nombre d’opérations simultanées à la valeur assignée. Si elle est définie sur une valeur inférieure à 0, le système détermine automatiquement le nombre d’opérations simultanées à exécuter. Comme le connecteur mappe chaque partition de collection à un exécuteur, cette valeur n’a aucun effet sur l’opération de lecture.        |
|query_maxbuffereditemcount     |    Définit le nombre maximal d’éléments qui peuvent être mis en mémoire tampon du côté client lors de l’exécution de requête en parallèle. S’il est défini sur une valeur qui est supérieure à 0, il limite le nombre d’éléments mis en mémoire tampon à la valeur assignée. S’il est défini sur une valeur inférieure à 0, le système détermine automatiquement le nombre d’éléments à mettre en mémoire tampon.     |
|query_enablescan    |   Définit l’option permettant d’activer les analyses sur les requêtes qui n’ont pas pu être prises en charge en raison de la désactivation de l’indexation sur les chemins demandés.       |
|query_disableruperminuteusage  |  Désactive les unités de requête (RU)/minute de capacité pour répondre à la requête s’il n’y a plus de RU standard provisionnées/seconde.       |
|query_emitverbosetraces   |   Définit l’option permettant d’autoriser les requêtes à émettre des traces détaillées pour l’investigation.      |
|query_pagesize  |   Définit la taille de la page des résultats de requête pour chaque demande de requête. Pour optimiser le débit, utilisez une page de grande taille afin de réduire le nombre d’allers-retours lors de l’extraction des résultats.      |
|query_custom  |  Définit la requête Azure Cosmos DB pour remplacer la requête par défaut lors de la récupération des données à partir d’Azure Cosmos DB. Notez que quand vous fournissez cette valeur, elle est également utilisée à la place d’une requête dotée de prédicats poussés vers le bas.     |

Selon le scénario, vous devez utiliser des valeurs de configuration différentes pour optimiser les performances et le débit. Notez que la clé de configuration ne respecte pas la casse pour l’instant, et que la valeur de configuration est toujours une chaîne.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Lire des données Twitter diffusées en streaming vers Azure Cosmos DB

Dans cette section, vous exécutez des requêtes Spark pour lire un flux de modification de données Twitter diffusées en streaming. Pendant que vous exécutez les requêtes dans cette section, assurez-vous que votre application de flux Twitter est en cours d’exécution et qu’elle pompe les données vers Azure Cosmos DB. Le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) héberge la version HTML du notebook. Téléchargez les fichiers du dépôt et accédez à `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html`. Vous pouvez importer le notebook dans votre compte Azure Databricks et mettre à jour l’URI du compte, la clé principale, la base de données et les noms de collection. Vous pouvez exécuter le notebook ou le créer comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace** (Espace de travail) > **Create** (Créer) > **Notebook**.  

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial et choisissez **Scala** comme langage. Dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

3. Mettez à jour les valeurs du point de terminaison, de la clé principale, de la base de données et de la collection pour vous connecter au compte.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Démarrez la lecture du flux de modification en streaming à l’aide de la commande spark.readStream.format() :

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Démarrez la diffusion en streaming de la requête à votre console :

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Java SDK prend en charge les valeurs suivantes pour le mappage de configuration :

|Paramètre  |Description  |
|---------|---------|
|readchangefeed   |  Indique que le contenu d’une collection est extrait du flux de modification CosmosDB. La valeur par défaut est false.       |
|changefeedqueryname |   Chaîne personnalisée pour identifier la requête. Le connecteur effectue le suivi séparément des jetons de continuation de collection pour les différentes requêtes de flux de modification. Si la valeur de readchangefeed est true, il s’agit d’une configuration obligatoire qui ne peut pas prendre de valeur vide.      |
|changefeedcheckpointlocation  |   Chemin au stockage de fichiers local pour conserver les jetons de continuation en cas de défaillances de nœud.      |
|changefeedstartfromthebeginning  |  Définit si le flux de modification doit démarrer à partir du début (true) ou à partir du point actuel (false). Par défaut, il démarre à la position actuelle (false).       |
|rollingchangefeed  |   Valeur booléenne indiquant si le flux de modification doit provenir de la dernière requête. La valeur par défaut est false, ce qui signifie que les modifications sont comptées à partir de la première lecture de la collection.      |
|changefeedusenexttoken  |   Valeur booléenne pour prendre en charge le traitement des scénarios d’échec. Elle indique que le lot de flux de modification actuel a été géré correctement. Le Resilient Distributed Dataset doit utiliser les jetons de continuation suivants pour obtenir le lot suivant de modifications.      |
| InferStreamSchema | Valeur booléenne qui indique si le schéma des données de streaming doit être échantillonné au début de la diffusion en streaming. Par défaut, cette valeur est définie sur true. Si ce paramètre est défini sur true, et que le schéma des données de streaming change après l’échantillonnage des données, les propriétés récemment ajoutées sont supprimées dans la trame des données de streaming. <br/><br/> Si vous souhaitez que la trame des données de streaming soit indépendante du schéma, définissez ce paramètre sur false. Dans ce mode, le corps des documents lus du flux de modification Azure Cosmos DB est wrappé dans une propriété de corps. Cette propriété est dans la trame des données de streaming résultante (en plus de valeurs de propriété système).
 |

### <a name="connection-settings"></a>Paramètres de connexion

Java SDK prend en charge les paramètres de connexion suivants :

|Paramètre  |Description  |
|---------|---------|
|ConnectionMode   |  Définit le mode de connexion que le DocumentClient interne doit utiliser pour communiquer avec Azure Cosmos DB. Les valeurs autorisées sont **DirectHttps** (valeur par défaut) et **Gateway**. Le mode de connexion DirectHttps achemine les requêtes directement aux partitions COSMOS DB et fournit quelques avantages de latence.       |
|connectionmaxpoolsize   |  Définit la valeur de la taille des pools de connexion qui est utilisée par DocumentClient interne. La valeur par défaut est 100.       |
|connectionidletimeout  |  Définit la valeur du délai d’expiration, en secondes, pour les connexions inactives. La valeur par défaut est 60.       |
|query_maxretryattemptsonthrottledrequests    |  Définit le nombre maximal de nouvelles tentatives. Vous utilisez cette valeur en cas d’échec d’une requête dû à une restriction du débit sur le client. Si elle n’est pas spécifiée, la valeur par défaut est de 1 000 nouvelles tentatives.       |
|query_maxretrywaittimeinseconds   |  Définit la durée maximale d’une nouvelle tentative en secondes. Elle est par défaut de 1 000 secondes.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Écrire des données Twitter dans Azure Cosmos DB 

Dans cette section, vous exécutez des requêtes Spark pour écrire un lot de données Twitter dans une nouvelle collection de la même base de données. Le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) héberge la version HTML du notebook. Téléchargez les fichiers du dépôt et accédez à `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html`. Vous pouvez importer le notebook dans votre compte Azure Databricks et mettre à jour l’URI du compte, la clé principale, la base de données et les noms de collection. Vous pouvez exécuter le notebook ou le créer comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace** (Espace de travail) > **Create** (Créer) > **Notebook**.  

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial et choisissez **Scala** comme langage. Dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

3. Mettez à jour les valeurs de point de terminaison, de clé principale, de base de données et de collection afin de vous connecter à la collection de bases de données pour lire et écrire des données Twitter.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Exécutez la requête pour obtenir le nombre de tweets par hashtags différents à partir des données mises en cache. 

   ```scala
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

5. Créez une trame de données pour les balises de tweets et enregistrez ces données dans la nouvelle collection. Après avoir exécuté le code suivant, vous pouvez revenir au portail et vérifier que les données sont écrites dans la collection. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Java SDK prend en charge les valeurs suivantes pour le mappage de configuration :

|Paramètre  |Description  |
|---------|---------|
| BulkImport | Valeur booléenne qui indique si les données doivent être importées au moyen de la bibliothèque BulkExecutor. Par défaut, cette valeur est définie sur true. |
|WritingBatchSize  |   Indique la taille de lot à utiliser quand vous écrivez des données dans la collection Azure Cosmos DB. <br/><br/> Si le paramètre BulkImport est défini sur true, le paramètre WritingBatchSize indique la taille de lot des documents fournis en tant qu’entrée à l’API importAll de la bibliothèque BulkExecutor. Par défaut, cette valeur est définie sur 100 000. <br/><br/> Si le paramètre BulkImport est défini sur false, le paramètre WritingBatchSize indique la taille de lot à utiliser quand vous écrivez des données dans la collection Azure Cosmos DB. Le connecteur envoie les requêtes createDocument et upsertDocument de manière asynchrone dans le lot. Tant que les ressources de cluster sont disponibles, plus la taille de lot est grande, plus le débit accessible est élevé. Spécifiez, en revanche, une taille de lot inférieure pour limiter le débit et la consommation de RU. Par défaut, la taille de lot d’écriture est définie sur 500.  |
|Upsert   |  Chaîne de valeur booléenne indiquant s’il faut utiliser upsertDocument au lieu de CreateDocument quand vous écrivez des données dans la collection Azure Cosmos DB.   |
| WriteThroughputBudget |  Chaîne d’entier qui représente le nombre de RU\s que vous souhaitez allouer à la tâche Spark d’ingestion en bloc par rapport au débit total alloué à la collection. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Écrire des données Twitter qui sont diffusées en streaming vers Azure Cosmos DB 

Dans cette section, vous exécutez des requêtes Spark pour écrire un flux de modification des données Twitter de streaming dans une nouvelle collection de la même base de données. Le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) héberge la version HTML du notebook. Téléchargez les fichiers du dépôt et accédez à `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html`. Vous pouvez importer le notebook dans votre compte Azure Databricks et mettre à jour l’URI du compte, la clé principale, la base de données et les noms de collection. Vous pouvez exécuter le notebook ou le créer comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace** (Espace de travail) > **Create** (Créer) > **Notebook**.  

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial et choisissez **Scala** comme langage. Dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

3. Mettez à jour les valeurs de point de terminaison, de clé principale, de base de données et de collection afin de vous connecter à la collection de bases de données pour lire et écrire des données Twitter.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Démarrez la lecture du flux de modification en streaming à l’aide de la commande spark.readStream.format() :
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Définissez la configuration de la collection de destination et démarrez la tâche de streaming avec la méthode writeStream.format() :

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

Java SDK prend en charge les valeurs suivantes pour le mappage de configuration :

|Paramètre  |Description  |
|---------|---------|
|Upsert   |  Chaîne de valeur booléenne indiquant s’il faut utiliser upsertDocument au lieu de CreateDocument quand vous écrivez des données dans la collection Azure Cosmos DB.   |
|checkpointlocation  |   Chemin au stockage de fichiers local pour conserver les jetons de continuation en cas de défaillances de nœud.   |
|WritingBatchSize  |  Indique la taille de lot à utiliser lors de l’écriture de données dans la collection Azure Cosmos DB. Le connecteur envoie les requêtes createDocument et upsertDocument de manière asynchrone dans le lot. Tant que les ressources de cluster sont disponibles, plus la taille de lot est grande, plus le débit accessible est élevé. Spécifiez, en revanche, une taille de lot inférieure pour limiter le débit et la consommation de RU. Par défaut, la taille de lot d’écriture est définie sur 500.  |


### <a name="considerations-when-using-java-sdk"></a>Considérations relatives à l’utilisation de Java SDK

La connexion de Spark à Azure Cosmos DB au moyen du SDK Java est recommandée dans les scénarios suivants Spark:

* Vous souhaitez utiliser Python ou Scala.  

* Vous avez une grande quantité de données à transférer entre Apache Spark et Azure Cosmos DB. Le SDK Java est plus performant que pyDocumentDB. Pour plus d’informations sur la différence en termes de performances des requêtes, consultez le [wiki sur les séries de tests de requête](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Étapes suivantes

Si cela n’est pas déjà fait, téléchargez le connecteur Spark-Azure Cosmos DB à partir du dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Explorez les ressources supplémentaires suivantes dans le dépôt :

* [Aggregations Examples](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples) (Exemples d’agrégations)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Exemples de scripts et Notebooks)

Vous pouvez également consulter [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guide sur Apache Spark SQL, les tableaux de données et les jeux de données) et l’article [Apache Spark sur Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
