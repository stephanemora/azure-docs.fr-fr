---
title: 'Tutoriel : Traiter des données à partir d’Azure Event Hubs avec Apache Spark dans Azure HDInsight '
description: Connectez Apache Spark dans Azure HDInsight à Azure Event Hubs et traitez les données de streaming.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 06/14/2018
ms.openlocfilehash: 9cdb5ae31e2743b5ebe877ddd8d6680423e3d9b2
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046250"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Tutoriel : Traiter les tweets à l’aide d’Azure Event Hubs et de Spark dans HDInsight

Dans ce tutoriel, vous allez apprendre à créer une application de streaming Apache Spark pour envoyer des tweets à un hub d’événements Azure et à créer une autre application pour lire les tweets à partir du hub d’événements. Pour obtenir une explication détaillée de la diffusion en continu Spark, voir la [présentation de la diffusion en continu Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight apporte les mêmes fonctionnalités de diffusion en continu à un cluster Spark sur Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Envoyer des messages à Azure Event Hub
> * Lire des messages à partir d’Azure Event Hub

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* **Consultez l’article [Didacticiel : charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Création d'une application Twitter

Pour recevoir un flux de tweets, vous créez une application dans Twitter. Suivez les instructions pour créer une application Twitter et notez les valeurs dont vous avez besoin pour effectuer ce tutoriel.

1. Accédez à la page de [gestion des applications Twitter](https://apps.twitter.com/).
2. Sélectionnez **Create New App** (Créer une application).
3. Renseignez les valeurs suivantes :

    - Name (Nom) : indiquez le nom de l’application. La valeur utilisée pour ce tutoriel est **HDISparkStreamApp0423**. Ce nom doit être unique.
    - Description : entrez une brève description de l’application. La valeur utilisée pour ce tutoriel est **A simple HDInsight Spark streaming application** (Application de streaming HDInsight Spark simple).
    - Website (Site web) : indiquez le site web de l’application. Il n’est pas nécessaire que ce soit un site web valide.  La valeur utilisée pour ce tutoriel est **http://www.contoso.com**.
    - Callback URL (URL de rappel) : vous pouvez laisser ce champ vide.

4. Sélectionnez **Yes, I have read and agree to the Twitter Developer Agreement** (Oui, j’ai lu et accepte le contrat de développement Twitter), puis sélectionnez **Create your Twitter application** (Créer votre application Twitter).
5. Sélectionnez l’onglet **Keys and Access Tokens** .
6. Sélectionnez **Create my access token** (Créer mon jeton d’accès) à la fin de la page.
7. Notez les valeurs suivantes à partir de la page.  Vous aurez besoin de ces valeurs plus loin dans le tutoriel :

    - **Consumer Key (API Key)** (Clé du client (clé API))    
    - **Consumer Secret (API Secret)** (Secret du client (secret API))  
    - **Access Token**
    - **Access Token Secret** (Secret du jeton d’accès)   

## <a name="create-an-azure-event-hub"></a>Création d'un hub d'événements Azure

Ce hub d’événements vous permet de stocker des tweets.

1. Connectez-vous au [Portail Azure](https://ms.portal.azure.com).
2. Sélectionnez **Créer une ressource** en haut à gauche de l’écran.
3. Sélectionnez **Internet des objets**, puis **Hubs d’événements**.

    ![Créer un Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Créer un Event Hub pour un exemple de diffusion en continu Spark")
4. Entrez les valeurs suivantes pour le nouvel espace de noms du hub d’événements :

    - **Nom** : entrez un nom pour le hub d’événements.  La valeur utilisée pour ce tutoriel est **myeventhubns20180403**.
    - **Niveau de prix** : sélectionnez **Standard**.
    - **Groupe de ressources** : vous pouvez créer un groupe de ressources ou en sélectionner le groupe de ressources pour le cluster Spark. 
    - **Emplacement** : sélectionnez le même **emplacement** que celui de votre cluster Apache Spark dans HDInsight pour réduire la latence et les coûts.

    ![Fournir un nom d’Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fournir un nom d’Event Hub pour un exemple de diffusion en continu Spark")
5. Sélectionnez **Créer** pour créer l’espace de noms.

6. Ouvrez l’espace de noms du hub d’événements à l’aide des instructions suivantes :

    1. À partir du portail Azure, sélectionnez **Tous les services**.
    2. Dans la zone de filtre, entrez **hubs d’événements**.
    3. Double-cliquez sur l’espace de noms que vous avez créé.
    4. Sélectionnez **+ Hub d’événements**.

6. Dans la liste d’espaces de noms Event Hubs, sélectionnez le nouvel espace de noms.      
5. Sélectionnez **Hubs d’événements**, puis sélectionnez **+ Hub d’événements** pour créer un hub d’événements.
  

6. Saisissez les valeurs suivantes :

    - Nom : donnez un nom à votre hub d’événements.
    - Nombre de partitions : 10
    - Rétention des messages : 1. 
   
    ![Fournir des détails d’Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fournir des détails d’Event Hub pour un exemple de diffusion en continu Spark")

7. Sélectionnez **Créer**.
8. Sélectionnez **Stratégies d’accès partagé** pour l’espace de noms (notez qu’il ne s’agit pas des stratégies d’accès partagé du hub d’événements), puis sélectionnez **RootManageSharedAccessKey**.
    
     ![Définir des stratégies d’Event Hub pour l’exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Définir des stratégies d’Event Hub pour l’exemple de diffusion en continu Spark")

9. Enregistrez les valeurs de **Clé primaire** et **Clé primaire de la chaîne de connexion** à utiliser plus tard dans le tutoriel.

     ![Afficher des clés de stratégie d’Event Hub pour l’exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Afficher des clés de stratégie d’Event Hub pour l’exemple de diffusion en continu Spark")


## <a name="send-tweets-to-the-event-hub"></a>Envoyer des tweets au hub d’événements

Vous devez créer un bloc-notes Jupyter et le nommer **SendTweetsToEventHub**. 

1. Exécutez le code suivant pour ajouter les bibliothèques Maven externes :

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Exécutez le code suivant pour envoyer des tweets à votre hub d’événements :

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Ouvrez le hub d’événements dans le portail Azure.  Dans **Vue d’ensemble**, certains graphiques doivent apparaître, indiquant les messages envoyés au hub d’événements.

## <a name="read-tweets-from-the-event-hub"></a>Lire des tweets à partir du hub d’événements

Vous devez créer un autre bloc-notes Jupyter et le nommer **ReadTweetsFromEventHub**. 

1. Exécutez le code suivant pour ajouter une bibliothèque Maven externe :

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Exécutez le code suivant pour lire des tweets à partir de votre hub d’événements :

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Supprimer les ressources

Avec HDInsight, vos données étant stockées dans Stockage Azure ou Azure Data Lake Store, vous pouvez supprimer un cluster de manière sécurisée s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Si vous prévoyez de suivre le tutoriel suivant immédiatement, vous souhaiterez peut-être conserver le cluster.

Ouvrez le cluster dans le portail Azure, puis sélectionnez **Supprimer**.

![Supprimer le cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Supprimer le cluster HDInsight")

Vous pouvez également sélectionner le nom du groupe de ressources pour ouvrir la page du groupe de ressources, puis sélectionner **Supprimer le groupe de ressources**. En supprimant le groupe de ressources, vous supprimez le cluster HDInsight Spark et le compte de stockage par défaut.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Lire un message à partir d’un hub d’événements.
Passez à l’article suivant pour voir comment créer une application d’apprentissage automatique. 

> [!div class="nextstepaction"]
> [Créer une application d’apprentissage automatique](./apache-spark-ipython-notebook-machine-learning.md)


