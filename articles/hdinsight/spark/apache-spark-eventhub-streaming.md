---
title: 'Didacticiel : Traiter des données à partir d’Azure Event Hubs avec Apache Spark dans HDInsight'
description: Didacticiel - Connectez Apache Spark dans Azure HDInsight à Azure Event Hubs et traitez les données de streaming.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: 2483ba22d3d502479e87ae385bcc837ec87a103c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735349"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Didacticiel : Traiter les tweets à l’aide d’Azure Event Hubs et d’Apache Spark dans HDInsight

Dans ce tutoriel, vous allez apprendre à créer une application de streaming [Apache Spark](https://spark.apache.org/) pour envoyer des tweets à un hub d’événements Azure et à créer une autre application pour lire les tweets à partir du hub d’événements. Pour obtenir une explication détaillée de la diffusion en continu Spark, voir la [présentation de la diffusion en continu Apache Spark](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight apporte les mêmes fonctionnalités de diffusion en continu à un cluster Spark sur Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Envoyer des messages à Azure Event Hub
> * Lire des messages à partir d’Azure Event Hub

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Un cluster Apache Spark sur HDInsight. Consultez [Créer un cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Connaissances sur l’utilisation des blocs-notes Jupyter Notebook avec Spark sur HDInsight. Pour plus d’informations, consultez [Charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight](./apache-spark-load-data-run-query.md).

* Un [compte Twitter](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Création d'une application Twitter

Pour recevoir un flux de tweets, vous créez une application dans Twitter. Suivez les instructions pour créer une application Twitter et notez les valeurs dont vous avez besoin pour effectuer ce tutoriel.

1. Accédez à la page de [gestion des applications Twitter](https://apps.twitter.com/).

1. Sélectionnez **Create New App** (Créer une application).

1. Renseignez les valeurs suivantes :

    |Propriété |Valeur |
    |---|---|
    |Nom|Indiquez le nom de l’application. La valeur utilisée pour ce tutoriel est **HDISparkStreamApp0423**. Ce nom doit être unique.|
    |Description|Entrez une brève description de l’application. La valeur utilisée pour ce tutoriel est **A simple HDInsight Spark streaming application** (Application de streaming HDInsight Spark simple).|
    |Website|Indiquez le site web de l’application. Il n’est pas nécessaire que ce soit un site web valide.  La valeur utilisée pour ce tutoriel est `http://www.contoso.com`.|
    |URL de rappel|Vous pouvez laisser cette valeur vide.|

1. Sélectionnez **Yes, I have read and agree to the Twitter Developer Agreement** (Oui, j’ai lu et accepte le contrat de développement Twitter), puis sélectionnez **Create your Twitter application** (Créer votre application Twitter).

1. Sélectionnez l’onglet **Keys and Access Tokens** .

1. Sélectionnez **Create my access token** (Créer mon jeton d’accès) à la fin de la page.

1. Notez les valeurs suivantes à partir de la page.  Vous aurez besoin de ces valeurs plus loin dans le tutoriel :

    - **Consumer Key (API Key)** (Clé du client (clé API))    
    - **Consumer Secret (API Secret)** (Secret du client (secret API))  
    - **Access Token**
    - **Access Token Secret** (Secret du jeton d’accès)   

## <a name="create-an-azure-event-hubs-namespace"></a>Créer un espace de noms Azure Event Hubs

Ce hub d’événements vous permet de stocker des tweets.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

2. Dans le menu de gauche, sélectionnez **Tous les services**.  

3. Sous **Internet des objets**, sélectionnez **Event Hubs**. 

    ![Créer un Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Créer un Event Hub pour un exemple de diffusion en continu Spark")

4. Sélectionnez **Ajouter**.

5. Entrez les valeurs suivantes pour le nouvel espace de noms Event Hubs :

    |Propriété |Valeur |
    |---|---|
    |Nom|entrez un nom pour le hub d’événements.  La valeur utilisée pour ce tutoriel est **myeventhubns20180403**.|
    |Niveau tarifaire|Sélectionnez **Standard**.|
    |Subscription|Sélectionnez votre abonnement approprié.|
    |Resource group|sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez **Créer** pour créer un groupe de ressources.|
    |Location|sélectionnez le même **emplacement** que celui de votre cluster Apache Spark dans HDInsight pour réduire la latence et les coûts.|
    |Activer la majoration automatique (facultatif) |L’augmentation automatique met automatiquement à l’échelle le nombre d’unités de débit affectées à votre espace de noms Event Hubs quand votre trafic dépasse la capacité des unités de débit qui lui sont assignées.  |
    |Augmentation automatique des unités de débit maximales (Facultatif)|Ce curseur ne s’affiche que si vous cochez **Activer l’augmentation automatique**.  |

    ![Fournir un nom d’Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fournir un nom d’Event Hub pour un exemple de diffusion en continu Spark")

6. Sélectionnez **Créer** pour créer l’espace de noms.  Le déploiement prendra quelques minutes.

## <a name="create-an-azure-event-hub"></a>Créer un hub d’événements Azure
Créez un hub d’événements une fois que l’espace de noms Event Hubs a été déployé.  À partir du portail :

1. Dans le menu de gauche, sélectionnez **Tous les services**.  

1. Sous **Internet des objets**, sélectionnez **Event Hubs**.  

1. Sélectionnez votre espace de noms Event Hubs dans la liste.  

1. Dans la page **Espace de noms Event Hubs**, sélectionnez **+ Event Hub**.  
1. Entrez les valeurs suivantes dans la page **Créer un Event Hub** :

    - **Nom** : donnez un nom à votre hub d’événements. 
 
    - **Nombre de partitions** : 10.  

    - **Rétention des messages** : 1.   
   
      ![Fournir des détails d’Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fournir des détails d’Event Hub pour un exemple de diffusion en continu Spark")

1. Sélectionnez **Create** (Créer).  Le déploiement doit se terminer en quelques secondes, et vous serez redirigé vers la page Espace de noms Event Hubs.

1. Sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**.

1. Sélectionnez **RootManageSharedAccessKey**.
    
     ![Définir des stratégies d’Event Hub pour l’exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Définir des stratégies d’Event Hub pour l’exemple de diffusion en continu Spark")

1. Enregistrez les valeurs de **Clé primaire** et **Clé primaire de la chaîne de connexion** à utiliser plus tard dans le tutoriel.

     ![Afficher des clés de stratégie d’Event Hub pour l’exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Afficher des clés de stratégie d’Event Hub pour l’exemple de diffusion en continu Spark")


## <a name="send-tweets-to-the-event-hub"></a>Envoyer des tweets au hub d’événements

Créez un bloc-notes Jupyter et nommez-le **SendTweetsToEventHub**. 

1. Exécutez le code suivant pour ajouter les bibliothèques Apache Maven externes :

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Modifiez le code ci-dessous en remplaçant `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` et `<TOKEN SECRET>` par les valeurs appropriées. Exécutez le code modifié pour envoyer des tweets à votre hub d’événements :

    ```scala
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

Créez un autre bloc-notes Jupyter et nommez-le **ReadTweetsFromEventHub**. 

1. Exécutez le code suivant pour ajouter la bibliothèque Apache Maven externe :

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Modifiez le code ci-dessous en remplaçant `<Event hub name>` et `<Event hub namespace connection string>` par les valeurs appropriées. Exécutez le code modifié pour lire des tweets à partir de votre hub d’événements :

    ```scala
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

## <a name="clean-up-resources"></a>Supprimer des ressources

Avec HDInsight, vos données étant stockées dans Stockage Azure ou Azure Data Lake Storage, vous pouvez supprimer un cluster de manière sécurisée s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Si vous prévoyez de suivre tout de suite le tutoriel suivant, vous souhaiterez peut-être conserver le cluster. Sinon, supprimez-le.

Ouvrez le cluster dans le portail Azure, puis sélectionnez **Supprimer**.

![Supprimer le cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Supprimer le cluster HDInsight")

Vous pouvez également sélectionner le nom du groupe de ressources pour ouvrir la page du groupe de ressources, puis sélectionner **Supprimer le groupe de ressources**. En supprimant le groupe de ressources, vous supprimez le cluster HDInsight Spark et le compte de stockage par défaut.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer une application de streaming Apache Spark pour envoyer des tweets à un hub d’événements Azure et à créer une autre application pour lire les tweets à partir du hub d’événements.  Passez à l’article suivant pour voir comment créer une application d’apprentissage automatique.

> [!div class="nextstepaction"]
> [Créer une application d’apprentissage automatique](./apache-spark-ipython-notebook-machine-learning.md)
