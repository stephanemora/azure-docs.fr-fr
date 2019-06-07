---
title: 'Didacticiel : Détection d’anomalie sur les données de streaming avec Azure Databricks'
description: Utilisez l’API de détecteur d’anomalies et Azure Databricks pour surveiller des anomalies dans vos données.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: a5790b5412023f06d9f9fd1d2ff61c11db4c53f3
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807467"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Didacticiel : Détection d’anomalie sur les données de streaming avec Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) est un service rapide, simple et collaborative analytique basée sur Apache Spark. L’API de détecteur d’anomalie, partie d’Azure Cognitive Services, offre un moyen de l’analyse de vos données de série chronologique. Utilisez ce didacticiel pour exécuter la détection d’anomalie sur un flux de données en quasi-temps réel à l’aide d’Azure Databricks. Vous allez recevoir des données de twitter à l’aide d’Azure Event Hubs et les importer dans Azure Databricks en utilisant le connecteur Spark Event Hubs. Vous utiliserez par la suite, l’API pour détecter les anomalies sur les données diffusées. 

L’illustration suivante montre le flux d’application :

![Azure Databricks avec Event Hubs et Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks avec Event Hubs et Cognitive Services")

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer une application Twitter pour accéder aux données de diffusion en continu
> * Créer des notebooks dans Azure Databricks
> * Joindre des bibliothèques pour Event Hubs et les API Twitter
> * Créer une ressource de détecteur d’anomalies et de récupérer la clé d’accès
> * Envoyer des tweets vers Event Hubs
> * Lire des tweets à partir d’Event Hubs
> * Exécuter la détection des anomalies sur des tweets

> [!Note]
> Ce didacticiel présente une approche à l’implémentation de l’architecture recommandée [architecture de solution](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) pour l’API de détecteur d’anomalies.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!Note]
> Ce didacticiel ne peut pas être effectué avec une clé de version d’évaluation gratuite pour l’API de détecteur d’anomalies. Pour utiliser un compte gratuit pour créer le cluster Azure Databricks, avant de créer le cluster, accédez à votre profil et définissez votre abonnement sur **paiement à l’utilisation**. Pour plus d’informations, consultez la page [Compte Azure gratuit](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Conditions préalables

- Un [espace de noms Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) et concentrateur d’événements.

- Le [chaîne de connexion](../../../event-hubs/event-hubs-get-connection-string.md) pour accéder à l’espace de noms Event Hubs. La chaîne de connexion doit avoir un format similaire à :

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Le nom de stratégie d’accès partagé et la stratégie de clé pour Event Hubs.

Consultez les Hubs d’événements Azure [quickstart](../../../event-hubs/event-hubs-create.md) pour plus d’informations sur la création d’un hub d’événements et d’espace de noms.

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Dans cette section, vous allez créer un espace de travail Azure Databricks à l’aide du [Azure portal](https://portal.azure.com/).

1. Dans le portail Azure, sélectionnez **Créer une ressource** >  **Analytique** > **Azure Databricks**.

    ![Databricks sur le portail Azure](../media/tutorials/azure-databricks-on-portal.png "Databricks sur le portail Azure")

3. Sous **Service Azure Databricks**, renseignez les valeurs suivantes pour créer un espace de travail Databricks :


    |Propriété  |Description  |
    |---------|---------|
    |**Nom de l’espace de travail**     | Renseignez un nom pour votre espace de travail Databricks.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../../../azure-resource-manager/resource-group-overview.md). |
    |**Lieu**     | Sélectionnez **est des États-Unis 2** ou l’un de toutes les autres régions disponibles. Consultez [des services Azure disponibles par région](https://azure.microsoft.com/regions/services/) pour la disponibilité régionale.        |
    |**Niveau tarifaire**     |  Choisissez entre **Standard** ou **Premium**. Ne choisissez pas **essai**. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Sélectionnez **Créer**.

4. La création de l’espace de travail dure quelques minutes. 

## <a name="create-a-spark-cluster-in-databricks"></a>Créer un cluster Spark dans Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. À partir du portail, sélectionnez **nouveau Cluster**.

    ![Databricks sur Azure](../media/tutorials/databricks-on-azure.png "Databricks sur Azure")

3. Dans le **nouveau Cluster** page, entrez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](../media/tutorials/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

    Acceptez toutes les valeurs par défaut autres que les suivantes :

   * Entrez un nom pour le cluster.
   * Pour cet article, créez un cluster avec le runtime **5.2**. Ne sélectionnez pas **5.3** runtime.
   * Assurez-vous que le **s’arrêtent après \_ \_ minutes d’inactivité** case à cocher est activée. Fournir une durée (en minutes) pour arrêter le cluster, si le cluster n’est pas utilisé.

     Sélectionnez **Créer un cluster**. 
4. La création du cluster prend plusieurs minutes. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

## <a name="create-a-twitter-application"></a>Création d'une application Twitter

Pour recevoir un flux de tweets, vous devez créer une application dans Twitter. Suivez les étapes pour créer une application Twitter et enregistrez les valeurs dont vous avez besoin pour terminer ce didacticiel.

1. À partir d’un navigateur web, accédez à [Gestion des applications Twitter](https://apps.twitter.com/), et sélectionnez **Créer une application**.

    ![Créer une application Twitter](../media/tutorials/databricks-create-twitter-app.png "Créer une application Twitter")

2. Sur la page **Créer une application**, renseignez les informations de la nouvelle application, puis sélectionnez **Créer votre application Twitter**.

    ![Détails de l’application de Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Détails de l’application de Twitter")

3. Sur la page de l’application, sélectionnez l’onglet **Clés et jetons d’accès** et copiez les valeurs de **Clé de consommation** et **Secret de consommation**. Sélectionnez aussi **Créer mon jeton d’accès** pour générer des jetons d’accès. Copiez les valeurs de **Jeton d’accès** et **Secret du jeton d’accès**.

    ![Détails de l’application de Twitter](../media/tutorials/twitter-app-key-secret.png "Détails de l’application de Twitter")

Enregistrez les valeurs que vous avez récupérées pour l’application Twitter. Vous aurez besoin de ces valeurs plus loin dans le didacticiel.

## <a name="attach-libraries-to-spark-cluster"></a>Joindre des bibliothèques au cluster Spark

Dans ce didacticiel, vous allez utiliser les API Twitter pour envoyer des tweets à Event Hubs. Vous allez aussi utiliser le [connecteur Apache Spark Event Hubs](https://github.com/Azure/azure-event-hubs-spark) pour lire et écrire des données dans Azure Event Hubs. Pour utiliser ces API au sein de votre cluster, ajoutez-les en tant que bibliothèques à Azure Databricks puis associez-les à votre cluster Spark. Les instructions suivantes indiquent comment ajouter les bibliothèques pour le **partagé** dossier dans votre espace de travail.

1. Dans l’espace de travail Azure Databricks, sélectionnez **Espace de travail**, puis cliquez avec le bouton droit sur **Partagé**. Dans le menu contextuel, sélectionnez **Créer** > **Bibliothèque**.

   ![Boîte de dialogue Ajouter une bibliothèque](../media/tutorials/databricks-add-library-option.png "Boîte de dialogue Ajouter une bibliothèque")

2. Dans la page nouvelle bibliothèque, pour **Source** sélectionnez **Maven**. Pour **coordonne**, saisissez les coordonnées pour le package que vous souhaitez ajouter. Voici les coordonnées Maven des bibliothèques utilisées dans ce didacticiel :

   * Connecteur Spark Event Hubs : `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API Twitter : `org.twitter4j:twitter4j-core:4.0.7`

     ![Renseigner des coordonnées Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Renseigner des coordonnées Maven")

3. Sélectionnez **Créer**.

4. Sélectionnez le dossier dans lequel vous avez ajouté la bibliothèque, puis sélectionnez le nom de la bibliothèque.

    ![Sélectionner la bibliothèque à ajouter](../media/tutorials/select-library.png "Sélectionner la bibliothèque à ajouter")

5. S’il n’existe aucun cluster dans la page de bibliothèque, sélectionnez **Clusters** et exécuter le cluster que vous avez créé. Attendez que l’état affiche « Running » et puis revenez à la page de la bibliothèque.
Dans la page de la bibliothèque, sélectionnez le cluster où vous souhaitez utiliser la bibliothèque, puis sélectionnez **installer**. Une fois que la bibliothèque est correctement associée au cluster, l’état devient immédiatement **installé**.

    ![Installer la bibliothèque au cluster](../media/tutorials/databricks-library-attached.png "bibliothèque d’installation à un cluster")

6. Répétez ces étapes pour le package Twitter, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Obtenir une clé d’accès Cognitive Services

Dans ce didacticiel, vous utilisez le [API du détecteur d’anomalies Azure Cognitive Services](../overview.md) pour exécuter la détection d’anomalie sur un flux de tweets en temps quasi réel. Avant d’utiliser les API, vous devez créer une ressource de détecteur d’anomalies sur Azure et récupérer une clé d’accès pour utiliser les API de détecteur d’anomalies.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Sélectionnez **+ Créer une ressource**.

3. Dans la place de marché Azure, sélectionnez **IA + Machine Learning** > **afficher tout** > **Cognitive Services - plus**  >  **Détecteur d’anomalies**. Vous pouvez également utiliser [ce lien](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) pour accéder à la **créer** directement à la boîte de dialogue.

    ![Créer une ressource de détecteur d’anomalies](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "ressource de créer un détecteur d’anomalies")

4. Dans la boîte de dialogue **Créer**, saisissez les valeurs suivantes :

    |Valeur |Description  |
    |---------|---------|
    |Nom     | Un nom pour la ressource de détecteur d’anomalies.        |
    |Abonnement     | L’abonnement Azure, la ressource sera associé.        |
    |Lieu     | Un emplacement Azure.        |
    |Niveau tarifaire     | Un niveau de tarification pour le service. Pour plus d’informations sur la tarification de détecteur d’anomalies, consultez [page de tarification](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Groupe de ressources     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou sélectionner un groupe existant.        |


     Sélectionnez **Créer**.

5. Une fois que la ressource est créée, à partir de la **vue d’ensemble** onglet, copiez et enregistrez le **point de terminaison** URL, comme indiqué dans la capture d’écran. Puis sélectionnez **afficher les clés d’accès**.

    ![Afficher les clés d’accès](../media/tutorials/cognitive-services-get-access-keys.png "Afficher les clés d’accès")

6. Sous **clés**, sélectionnez l’icône de copie par rapport à la clé que vous souhaitez utiliser. Enregistrez la clé d’accès.

    ![Copier les clés d’accès](../media/tutorials/cognitive-services-copy-access-keys.png "Copier les clés d’accès")

## <a name="create-notebooks-in-databricks"></a>Créer des notebooks dans Databricks

Dans cette section, vous allez créer deux notebooks dans l’espace de travail Databricks avec les noms suivants

- **SendTweetsToEventHub** : un notebook producteur à utiliser pour obtenir des tweets de Twitter et les diffuser dans Event Hubs.
- **AnalyzeTweetsFromEventHub** -un notebook consommateur à utiliser pour lire les tweets à partir de concentrateurs d’événements et exécuter la détection des anomalies.

1. Dans l’espace de travail Azure Databricks, sélectionnez **espace de travail** dans le volet gauche. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** et **Notebook**.

    ![Créer un notebook dans Databricks](../media/tutorials/databricks-create-notebook.png "Créer un notebook dans Databricks")

2. Dans le **créer un Notebook** boîte de dialogue, entrez **SendTweetsToEventHub** comme nom, sélectionnez **Scala** en tant que langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Créer un notebook dans Databricks](../media/tutorials/databricks-notebook-details.png "Créer un notebook dans Databricks")

    Sélectionnez **Créer**.

3. Répétez les étapes pour créer le notebook **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Envoyer des tweets vers Event Hubs

Dans le notebook **SendTweetsToEventHub**, collez le code suivant, et remplacez l'espace réservé par les valeurs de votre espace de noms Event Hubs et de l'application Twitter que vous avez créés précédemment. Ce bloc-notes extrait l’heure de création et le nombre de « Like » s à partir de tweets avec le mot clé « Azure » et ceux en tant qu’événements diffuser dans Event Hubs en temps réel.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Appuyez sur **Maj+Entrée** pour exécuter le notebook. Vous verrez alors une sortie, comme montré dans l’extrait de code suivant. Chaque événement dans la sortie est une combinaison de timestamp et nombre de « Like » s ingéré par Event Hubs.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Lire des tweets à partir d’Event Hubs

Dans le **AnalyzeTweetsFromEventHub** bloc-notes, collez le code suivant et remplacez l’espace réservé avec des valeurs pour votre ressource de détecteur d’anomalies que vous avez créé précédemment. Ce notebook lit les tweets que vous avez diffusés précédemment dans Event Hubs à l’aide du notebook **SendTweetsToEventHub**.

Tout d’abord, écrire un client pour appeler le détecteur d’anomalies. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Appuyez sur **Maj+Entrée** pour exécuter le notebook. Vous verrez alors une sortie, comme montré dans l’extrait de code suivant.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Ensuite, préparez une fonction d’agrégation pour un usage futur.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Appuyez sur **Maj+Entrée** pour exécuter le notebook. Vous verrez alors une sortie, comme montré dans l’extrait de code suivant.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Puis charger des données à partir du concentrateur d’événements pour la détection des anomalies. Remplacez l’espace réservé avec des valeurs pour votre Azure Event Hubs que vous avez créé précédemment.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

La sortie ressemble maintenant à l’image suivante. Notez que votre date de la table peut être différente de la date dans ce didacticiel que les données sont en temps réel.
![Hub d’événement à partir de données de charge](../media/tutorials/load-data-from-eventhub.png "charge données à partir d’Event Hub")

Vous avez diffusé en continu les données issues d'Azure Event Hubs sur Azure Databricks presque en temps réel à l'aide du connecteur Event Hubs pour Apache Spark. Pour plus d’informations sur la façon d’utiliser le connecteur Event Hubs pour Spark, consultez la [documentation relative au connecteur](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Exécuter la détection des anomalies sur des tweets

Dans cette section, vous exécutez la détection d’anomalie sur les tweets reçus à l’aide de l’API de détecteur d’anomalies. Dans cette section, vous allez ajouter les extraits de code au même notebook **AnalyzeTweetsFromEventHub**.

Tout d’abord, pour faire la détection d’anomalie, vous devez regrouper votre nombre de métriques par heure.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
La sortie ressemble maintenant à des extraits de code suivants.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Puis obtenir le résultat de sortie agrégées à Delta. Détection d’anomalie exigeant une fenêtre d’historique plus de temps, nous utilisons le Delta de conserver les données d’historique pour le point que vous souhaitez détecter. Remplacez le « [espace réservé : nom de la table] » avec un nom de table qualifié Delta doit être créé (par exemple, « tweets »). Remplacez « [espace réservé : nom du dossier pour les points de contrôle] » avec une valeur de chaîne qui est unique à chaque fois que vous exécutez ce code (par exemple, « etl-de-eventhub-20190605 »).
Pour en savoir plus sur Lake Delta sur Azure Databricks, reportez-vous à [Delta Lake Guide](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Remplacez le « [espace réservé : nom de la table] » portant le même nom de table Delta, vous avez sélectionné ci-dessus.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
La sortie comme indiqué ci-dessous : 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Désormais les données de série agrégées de temps en permanence ingérées par le Delta. Ensuite, vous pouvez planifier une tâche toutes les heures pour détecter les anomalies du dernier point. Remplacez le « [espace réservé : nom de la table] » portant le même nom de table Delta, vous avez sélectionné ci-dessus.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Résultat comme indiqué ci-dessous : 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+


That's it! Using Azure Databricks, you have successfully streamed data into Azure Event Hubs, consumed the stream data using the Event Hubs connector, and then run anomaly detection on streaming data in near real time.
Although in this tutorial, the granularity is hourly, you can always change the granularity to meet your need. 

## Clean up resources

After you have finished running the tutorial, you can terminate the cluster. To do so, in the Azure Databricks workspace, select **Clusters** from the left pane. For the cluster you want to terminate, move the cursor over the ellipsis under **Actions** column, and select the **Terminate** icon and then select **Confirm**.

![Stop a Databricks cluster](../media/tutorials/terminate-databricks-cluster.png "Stop a Databricks cluster")

If you don't manually terminate the cluster it will automatically stop, provided you selected the **Terminate after \_\_ minutes of inactivity** checkbox while creating the cluster. In such a case, the cluster will automatically stop if it has been inactive for the specified time.

## Next steps

In this tutorial, you learned how to use Azure Databricks to stream data into Azure Event Hubs and then read the streaming data from Event Hubs in real time. Advance to the next tutorial to learn how to call the Anomaly Detector API and visualize anomalies using Power BI desktop. 

> [!div class="nextstepaction"]
>[Batch anomaly detection with Power BI desktop](batch-anomaly-detection-powerbi.md)
