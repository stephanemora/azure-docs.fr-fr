---
title: Connexion d’Apache Spark à Azure Cosmos DB | Microsoft Docs
description: Grâce à ce didacticiel, vous en apprendrez davantage sur le connecteur Spark-Azure Cosmos DB, qui vous permet de connecter Apache Spark à Azure Cosmos DB pour réaliser des agrégations distribuées et des opérations de sciences des données sur le système de base de données mutualisé conçu pour le cloud de Microsoft, qui est distribué dans le monde entier.
keywords: apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: cae66a40882231f7762af29cdeaaf658dd2aa968
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113945"
---
# <a name="accelerate-real-time-big-data-analytics-by-using-the-spark-to-azure-cosmos-db-connector"></a>Accélérer l’analytique en temps réel des données volumineuses au moyen du connecteur Spark-Azure Cosmos DB
 
Le connecteur Spark-Azure Cosmos DB permet à Azure Cosmos DB de jouer le rôle d’entrée ou de sortie pour les tâches Apache Spark. Connecter [Spark](http://spark.apache.org/) à [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) accélère votre capacité à résoudre les problèmes liés aux opérations de sciences de données dynamiques, sachant que vous pouvez utiliser Azure Cosmos DB pour conserver et interroger rapidement des données. Le connecteur Spark-Azure Cosmos DB utilise efficacement les index gérés natifs d’Azure Cosmos DB. Les index activent des colonnes modifiables lorsque vous effectuez des analyses et opérez un filtrage de prédicat sur des données globalement distribuées qui changent rapidement, allant de l’Internet des objets (IoT) aux scénarios de sciences et d’analyse des données.

En savoir plus sur le connecteur Spark-Azure Cosmos DB dans cette vidéo :

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Composants du connecteur

Le connecteur Spark-Azure Cosmos DB utilise les composants suivants :

* [Azure Cosmos DB](http://documentdb.com) permet à ses utilisateurs d’approvisionner et d’adapter de façon élastique leurs capacités de débit et de stockage partout dans le monde.  

* [Apache Spark](http://spark.apache.org/) est un puissant moteur de traitement Open Source axé sur la vitesse, la facilité d’utilisation et des analyses sophistiquées.  

* [Cluster Apache Spark sur Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) pour exécuter les travaux spark sur le cluster spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Connecter Apache Spark à Azure Cosmos DB

Vous pouvez choisir entre deux approches pour connecter Apache Spark et Azure Cosmos DB :

1. En utilisant [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), un connecteur Python pour spark-Cosmos DB, également appelé « pyDocumentDB ».  

2. En utilisant [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java), un connecteur Java pour spark-Cosmos DB.


**Versions prises en charge**

| Composant | Version |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Version du runtime Databricks | > 3.4 |
| Kit SDK Java SQL Azure Cosmos DB | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Se connecter à l’aide de Python ou de pyDocumentDB SDK

L’image suivante illustre l’architecture de l’implémentation de pyDocumentDB SDK :

![Flux de données Spark-Azure Cosmos DB via pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Flux de données

Le flux de données de l’implémentation de pyDocumentDB se présente de la façon suivante :

* Le nœud principal de spark se connecte au nœud de passerelle Azure Cosmos DB par le biais de pyDocumentDB. Un utilisateur ne spécifie que les connexions spark et Azure Cosmos DB. Les connexions aux nœuds principal et de passerelle sont transparentes pour l’utilisateur.  

* Le nœud de passerelle effectue la requête sur Azure Cosmos DB, sachant que la requête s’exécute par la suite sur les partitions de la collection dans les nœuds de données. La réponse à ces requêtes est retournée au nœud de passerelle, puis le jeu de résultats est retourné au nœud principal spark.  

* Toutes les requêtes suivantes (par exemple, sur une trame de données spark) sont envoyées aux nœuds de travail Spark à des fins de traitement.  

La communication entre spark et Azure Cosmos DB est limitée au nœud principal spark et aux nœuds de passerelle Azure Cosmos DB. Les requêtes sont opérées aussi rapidement que la couche de transport entre ces deux nœuds le permet.

Exécutez les étapes suivantes pour connecter spark à Azure Cosmos DB avec pyDocumentDB SDK :

1. Créez un [espace de travail Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) et un [cluster spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (runtime Databricks version 4.0 comprenant Apache Spark 2.3.0, Scala 2.11) dans cet espace de travail.  

2. Une fois le cluster créé et en cours d’exécution, accédez à **Workspace (Espace de travail)** > **Create (Créer)** > **Library (Bibliothèque)**.  
3. Dans la boîte de dialogue New Library (Nouvelle bibliothèque), choisissez **Upload Python Egg or PyPi** (Charger Python Egg ou PyPi) comme source, indiquez **pydocumentdb** comme nom et sélectionnez **Install Library** (Installer la bibliothèque). Le kit PyDocumentdb SDK étant déjà publié sur les packages pip, vous pouvez le trouver et l’installer. 

   ![Créer et attacher une bibliothèque](./media/spark-connector/create-library.png)

4. Lorsque la bibliothèque est installée, attachez-la au cluster que vous avez créé précédemment.  

5. Accédez ensuite à **Workspace (Espace de travail)** > **Create (Créer)** > **Notebook (Notebook)**.  

6. Dans la boîte de dialogue **Create Notebook (Créer un notebook)**, entrez un nom convivial et choisissez **Python** comme langage. Dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create (Créer)**.  

7. La simplicité du transport de communication rend relativement simple l’exécution d’une requête de spark à Azure Cosmos DB à l’aide de pyDocumentDB. Exécutez ensuite quelques requêtes spark en utilisant les exemples de données de vols hébergés dans le compte Cosmos DB « doctorwho » qui est accessible publiquement. La version HTML du notebook est hébergée dans le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Vous devez télécharger les fichiers du dépôt et accéder à `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html` ; vous pouvez importer le notebook dans votre compte Azure Databricks et l’exécuter. La section suivante explique de façon détaillée les fonctionnalités des blocs de code.

L’extrait de code suivant montre comment importer pyDocumentDB SDK et exécuter une requête dans le contexte spark. Comme indiqué dans l’extrait de code, pyDocumentDB SDK contient les paramètres de connexion nécessaires pour se connecter au compte Azure Cosmos DB. Il importe les bibliothèques demandées, configure la clé principale et l’hôte pour créer le client Azure Cosmos DB (pydocumentdb.document_client).


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

Ensuite, vous pouvez exécuter des requêtes, l’extrait de code suivant se connecte à la collection airports.codes dans le compte DoctorWho et exécute une requête pour extraire les villes de l’état de Washington qui sont équipées d’un aéroport. 

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

Après l’exécution de la requête, le résultat « query_iterable.QueryIterable » est converti en une liste Python, qui est ensuite transformée en trame de données spark. 

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

## <a name="considerations-when-using-pydocumentdb-sdk"></a>Considérations relatives à l’utilisation de pyDocumentDB SDK
La connexion de spark à Azure Cosmos DB au moyen de pyDocumentDB SDK est recommandée dans les scénarios suivants :

* Vous souhaitez utiliser Python.  

* Un jeu de résultats relativement petit est retourné d’Azure Cosmos DB à spark. Notez que le jeu de données sous-jacent dans Azure Cosmos DB peut être très volumineux, et que vous appliquez des filtres ou exécutez des filtres de prédicat sur votre source Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Se connecter à l’aide de Java SDK

L’image suivante illustre l’architecture de l’implémentation d’Azure Cosmos DB SQL Java SDK et le déplacement les données entre les nœuds de travail spark :

![Flux de données dans le connecteur Spark-Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Flux de données

Le flux de données de l’implémentation de Java SDK se présente de la façon suivante :

* Le nœud principal spark se connecte au nœud de passerelle Azure Cosmos DB pour obtenir le mappage de partitions. Un utilisateur spécifie uniquement les connexions spark et Azure Cosmos DB. Les connexions aux nœuds principal et de passerelle sont transparentes pour l’utilisateur.  

* Ces informations sont retournées au nœud principal spark. À ce stade, vous devez être en mesure d’analyser la requête pour déterminer les partitions (et leurs emplacements) auxquelles vous devez accéder dans Azure Cosmos DB.  

* Ces informations sont transmises aux nœuds de travail spark.  

* Les nœuds de travail spark se connectent directement aux partitions Azure Cosmos DB pour extraire les données et les retourner aux partitions spark dans les nœuds de travail.  

La communication entre spark et Azure Cosmos DB est beaucoup plus rapide, car les données sont déplacées entre les nœuds de travail spark et les nœuds de données Azure Cosmos DB (partitions). Dans ce document, vous envoyez des exemples de données Twitter au compte Azure Cosmos DB et exécutez des requêtes spark en utilisant ces données. Utilisez les étapes suivantes pour écrire des exemples de données Twitter dans Azure Cosmos DB :

1. Créez un [compte API SQL Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) et [ajoutez une collection](create-sql-api-dotnet.md#add-a-collection) au compte.  

2. Téléchargez l’exemple [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) à partir de GitHub, qui est utilisé pour écrire des exemples de données Twitter dans Azure Cosmos DB.  

3. Ouvrez une invite de commandes et installez les modules Tweepy et pyDocumentdb en exécutant les commandes suivantes :

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extrayez le contenu de l’exemple de flux Twitter et ouvrez le fichier config.py. Mettez à jour les valeurs de masterKey, host, databaseId, collectionId et preferredLocations.  

5. Accédez à `http://apps.twitter.com/` et enregistrez le script du flux Twitter en tant que nouvelle application. Après avoir choisi un nom et une application pour votre application, vous recevez les valeurs de **consumer key (clé du consommateur), consumer secret (clé secrète du consommateur), access token (jeton d’accès) et access token secret (clé secrète du jeton d’accès)**. Copiez ces valeurs et mettez-les à jour dans le fichier config.py pour fournir l’accès de l’application par programmation à Twitter.   

6. Enregistrez le fichier config.py. Ouvrez l’invite de commandes et exécutez l’application python à l’aide de la commande suivante :

   ```bash
   Python driver.py
   ```

7. Accédez à la collection Azure Cosmos DB dans le portail et vérifiez que les données Twitter sont écrites dans la collection.

### <a name="find-and-attach-java-sdk-to-the-spark-cluster"></a>Rechercher et attacher Java SDK au cluster spark

1. Créez un [espace de travail Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) et un [cluster spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (runtime Databricks version 4.0 comprenant Apache Spark 2.3.0, Scala 2.11) dans cet espace de travail.  

2. Une fois le cluster créé et en cours d’exécution, accédez à **Workspace (Espace de travail)** > **Create (Créer)** > **Library (Bibliothèque)**.  

3. Dans la boîte de dialogue New Library (Nouvelle bibliothèque), choisissez **Maven Coordinate** (Coordonnées Maven) comme source, indiquez la valeur de coordonnée **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0** et sélectionnez **Create Library** (Créer la bibliothèque). Les dépendances de Maven sont résolues, et le package est ajouté à votre espace de travail. Dans le format de coordonnées Maven ci-dessus, 2.3.0 représente la version de spark, 2.11 la version de scala, et 1.2.0 la version du connecteur Azure Cosmos DB. 

4. Lorsque la bibliothèque est installée, attachez-la au cluster que vous avez créé précédemment. 

Cet article illustre l’utilisation du connecteur spark avec Java SDK dans les scénarios suivants :

* Lire des données Twitter à partir d’Azure Cosmos DB  

* Lire des données Twitter qui sont diffusées en continu vers Azure Cosmos DB  

* Écrire des données Twitter dans Azure Cosmos DB 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Lire des données Twitter à partir d’Azure Cosmos DB
 
Dans cette section, vous exécutez des requêtes spark pour lire un lot de données Twitter à partir d’Azure Cosmos DB. La version HTML du notebook est hébergée dans le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Vous devez télécharger les fichiers du dépôt et accéder à `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html` ; vous pouvez importer le notebook dans votre compte Azure Databricks, mettre à jour l’URI, la clé principale, la base de données, les noms de collection pour le compte et l’exécuter, ou vous pouvez créer le notebook comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace (Espace de travail)** > **Create (Créer)** > **Notebook (Notebook)**. 

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial, choisissez **Python** comme langage, puis dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

3. Mettez à jour les valeurs de point de terminaison, de clé principale, de base de données et de collection pour vous connecter au compte et lire des tweets au moyen de la commande spark.read.format().

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
|query_maxdegreeofparallelism  | Définit le nombre d’opérations simultanées qui sont exécutées côté client lors de l’exécution de la requête en parallèle. S’il est défini sur une valeur qui est supérieure à 0, il limite le nombre d’opérations simultanées à la valeur assignée. Si elle est définie sur une valeur inférieure à 0, le système détermine automatiquement le nombre d’opérations simultanées à exécuter. Comme le connecteur mappe chaque partition de collection avec un exécuteur, cette valeur n’a aucun effet sur l’opération de lecture.        |
|query_maxbuffereditemcount     |    Définit le nombre maximal d’éléments qui peuvent être mis en mémoire tampon du côté client lors de l’exécution de requête en parallèle. S’il est défini sur une valeur qui est supérieure à 0, il limite le nombre d’éléments mis en mémoire tampon à la valeur assignée. S’il est défini sur une valeur inférieure à 0, le système détermine automatiquement le nombre d’éléments à mettre en mémoire tampon.     |
|query_enablescan    |   Définit l’option permettant d’activer les analyses sur les requêtes qui n’ont pas pu être prises en charge en raison de la désactivation de l’indexation sur les chemins demandés.       |
|query_disableruperminuteusage  |  Désactive les unités de requête (RU)/minute de capacité pour répondre à la requête s’il n’y a plus de RU standard provisionnées/seconde.       |
|query_emitverbosetraces   |   Définit l’option permettant d’autoriser les requêtes à émettre des traces détaillées pour l’investigation.      |
|query_pagesize  |   Définit la taille de la page des résultats de requête pour chaque demande de requête. Pour optimiser le débit, utilisez une page de grande taille afin de réduire le nombre d’allers-retours lors de l’extraction des résultats de requêtes.      |
|query_custom  |  Définit la requête Azure Cosmos DB pour remplacer la requête par défaut lors de la récupération des données à partir d’Azure Cosmos DB. Notez que lorsque cette valeur est fournie, elle est également utilisée à la place d’une requête dotée de prédicats poussés vers le bas.     |

Selon le scénario, des valeurs de configuration différentes doivent être utilisées pour optimiser les performances et le débit. Notez que la clé de configuration ne respecte pas la casse pour l’instant, et que la valeur de configuration est toujours une chaîne.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Lire des données Twitter qui sont diffusées en continu vers Azure Cosmos DB

Dans cette section, vous exécutez des requêtes spark pour lire un flux de modification de données Twitter diffusées en continu. Pendant que vous exécutez les requêtes dans cette section, assurez-vous que votre application de flux Twitter est en cours d’exécution et qu’elle pompe les données vers Azure Cosmos DB. La version HTML du notebook est hébergée dans le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Vous devez télécharger les fichiers du dépôt et accéder à `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html` ; vous pouvez importer le notebook dans votre compte Azure Databricks, mettre à jour l’URI, la clé principale, la base de données, les noms de collection pour le compte et l’exécuter, ou vous pouvez créer le notebook comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace (Espace de travail)** > **Create (Créer)** > **Notebook (Notebook)**.  

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial, choisissez **Scala** comme langage, puis dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

3. Mettez à jour les valeurs de point de terminaison, de clé principale, de base de données et de collection pour la connexion au compte.

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
4. Démarrez la lecture du flux de modification en tant que flux de données en continu à l’aide de la commande spark.readStream.format() :

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Démarrez la diffusion en continu de la requête vers la console :

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
|rollingchangefeed  |   Valeur booléenne indiquant si le flux de modification doit provenir de la dernière requête. La valeur par défaut est false, ce qui signifie que les modifications seront comptées à partir de la première lecture de la collection.      |
|changefeedusenexttoken  |   Valeur booléenne pour prendre en charge le traitement des scénarios d’échec. Elle sert à indiquer que le traitement du flux de modification actuel a été géré correctement et que le RDD doit utiliser les jetons de continuation suivants pour obtenir le traitement suivant des changements.      |
| InferStreamSchema | Valeur booléenne qui indique si le schéma des données de streaming doit être échantillonné au début de la diffusion en continu. Par défaut, cette valeur est définie sur true. Si ce paramètre est défini sur true, et que le schéma des données de streaming change après l’échantillonnage des données, les propriétés récemment ajoutées sont supprimées dans la trame des données de streaming. <br/><br/> Si vous souhaitez que la trame des données de streaming soit indépendante du schéma, définissez ce paramètre sur false. Dans ce mode, le corps des documents lus à partir du flux de modification Azure Cosmos DB est wrappé dans une propriété « body », à l’intérieur de la trame des données de streaming obtenue, à l’exception des valeurs de propriété système.
 |

### <a name="connection-settings"></a>Paramètres de connexion

Java SDK prend en charge les paramètres de connexion suivants :

|Paramètre  |Description  |
|---------|---------|
|ConnectionMode   |  Définit le mode de connexion que le DocumentClient interne doit utiliser pour communiquer avec Azure Cosmos DB. Les valeurs autorisées sont **DirectHttps** (valeur par défaut) et **Gateway**. Le mode de connexion DirectHttps achemine les requêtes directement aux partitions COSMOS DB et fournit quelques avantages de latence.       |
|connectionmaxpoolsize   |  Définit la valeur de la taille des pools de connexion qui est utilisée par DocumentClient interne. La valeur par défaut est 100.       |
|connectionidletimeout  |  Définit la valeur du délai d’expiration pour les connexions inactives en secondes. La valeur par défaut est 60.       |
|query_maxretryattemptsonthrottledrequests    |  Définit le nombre maximal de nouvelles tentatives. Cette valeur est utilisée en cas d’échec d’une requête dû à une restriction du débit sur le client. Si elle n’est pas spécifiée, la valeur par défaut est de 1 000 nouvelles tentatives.       |
|query_maxretrywaittimeinseconds   |  Définit la durée maximale d’une nouvelle tentative en secondes. Elle est par défaut de 1 000 secondes.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Écrire des données Twitter dans Azure Cosmos DB 

Dans cette section, vous exécutez des requêtes spark pour écrire un lot de données Twitter dans une nouvelle collection de la même base de données. La version HTML du notebook est hébergée dans le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Vous devez télécharger les fichiers du dépôt et accéder à `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html` ; vous pouvez importer le notebook dans votre compte Azure Databricks, mettre à jour l’URI, la clé principale, la base de données, les noms de collection pour le compte et l’exécuter, ou vous pouvez créer le notebook comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace (Espace de travail)** > **Create (Créer)** > **Notebook (Notebook)**.  

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial, choisissez **Scala** comme langage, puis dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

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
|WritingBatchSize  |   Indique la taille de lot à utiliser lors de l’écriture de données dans la collection Azure Cosmos DB. <br/><br/> Si le paramètre BulkImport est défini sur true, le paramètre WritingBatchSize indique la taille de lot des documents fournis en tant qu’entrée à l’API importAll de la bibliothèque BulkExecutor. Par défaut, cette valeur est définie sur 100 000. <br/><br/> Si le paramètre BulkImport est défini sur false, le paramètre WritingBatchSize indique la taille de lot à utiliser lors de l’écriture dans la collection Azure Cosmos DB. Le connecteur envoie les requêtes createDocument/upsertDocument de manière asynchrone dans le lot. Tant que les ressources de cluster sont disponibles, plus la taille de lot est grande, plus le débit accessible est élevé. Spécifiez, en revanche, une taille de lot inférieure pour limiter le débit et la consommation de RU. Par défaut, la taille de lot d’écriture est définie sur 500.  |
|Upsert   |  Chaîne de valeur booléenne indiquant si upsertDocument doit être utilisé au lieu de CreateDocument lors de l’écriture dans la collection CosmosDB.   |
| WriteThroughputBudget |  Chaîne d’entier qui représente le nombre de RU\s que vous souhaitez allouer à la tâche spark d’ingestion en bloc par rapport au débit total alloué à la collection. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Écrire des données Twitter qui sont diffusées en continu vers Azure Cosmos DB 

Dans cette section, vous exécutez des requêtes spark pour écrire un flux de modification des données Twitter de streaming dans une nouvelle collection de la même base de données. La version HTML du notebook est hébergée dans le dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Vous devez télécharger les fichiers du dépôt et accéder à `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html` ; vous pouvez importer le notebook dans votre compte Azure Databricks, mettre à jour l’URI, la clé principale, la base de données, les noms de collection pour le compte et l’exécuter, ou vous pouvez créer le notebook comme suit :

1. Accédez à votre compte Azure Databricks et sélectionnez **Workspace (Espace de travail)** > **Create (Créer)** > **Notebook (Notebook)**.  

2. Dans la boîte de dialogue **Create Notebook** (Créer un notebook), entrez un nom convivial, choisissez **Scala** comme langage, puis dans la liste déroulante, sélectionnez le cluster que vous avez créé précédemment et sélectionnez **Create** (Créer).  

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
4. Démarrez la lecture du flux de modification en tant que flux de données en continu à l’aide de la commande spark.readStream.format() :
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Définissez la configuration de la collection de destination et démarrez la tâche de streaming avec la méthode writeStream.format() :

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
|Upsert   |  Chaîne de valeur booléenne indiquant si upsertDocument doit être utilisé au lieu de CreateDocument lors de l’écriture dans la collection CosmosDB.   |
|checkpointlocation  |   Chemin au stockage de fichiers local pour conserver les jetons de continuation en cas de défaillances de nœud.   |
|WritingBatchSize  |  Indique la taille de lot à utiliser lors de l’écriture de données dans la collection Azure Cosmos DB. Le connecteur envoie les requêtes createDocument/upsertDocument de manière asynchrone dans le lot. Tant que les ressources de cluster sont disponibles, plus la taille de lot est grande, plus le débit accessible est élevé. Spécifiez, en revanche, une taille de lot inférieure pour limiter le débit et la consommation de RU. Par défaut, la taille de lot d’écriture est définie sur 500.  |


## <a name="considerations-when-using-java-sdk"></a>Considérations relatives à l’utilisation de Java SDK

La connexion de spark à Azure Cosmos DB au moyen de Java SDK est recommandée dans les scénarios suivants :

* Vous souhaitez utiliser Python et/ou Scala.  

* Vous avez une grande quantité de données à transférer entre Apache Spark et Azure Cosmos DB, Java SDK offre des performances supérieures par rapport à pyDocumentDB. Pour vous donner une idée de la différence en termes de performances de requête, consultez le [wiki sur les séries de tests de requête](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas encore fait, téléchargez le connecteur Spark-Azure Cosmos DB à partir du dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) et explorez les ressources supplémentaires dans le dépôt :

* [Distributed Aggregations Examples](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples) (Exemples d’agrégations distribuées)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Exemples de scripts et blocs-notes)

Vous pouvez également consulter [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guide sur Apache Spark SQL, les tableaux de données et les jeux de données) et l’article [Apache Spark sur Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
