---
title: 'Tutoriel : Diffuser en continu des données dans Azure Databricks à l’aide d’Event Hubs '
description: Apprenez à utiliser Azure Databricks avec Event Hubs pour ingérer des données de diffusion en continu de Twitter et la lecture des données en temps quasi-réel.
services: azure-databricks
author: lenadroid
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 12/08/2019
ms.author: alehall
ms.openlocfilehash: 9cf1a7a1b9c6d0f35bf3cc9151e30141fd432a54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75410854"
---
# <a name="tutorial-stream-data-into-azure-databricks-using-event-hubs"></a>Tutoriel : Diffuser en continu des données dans Azure Databricks à l’aide d’Event Hubs

Dans ce didacticiel, vous connectez un système d’ingestion des données à Azure Databricks pour diffuser en continu des données en temps quasi-réel dans un cluster Apache Spark. Vous allez configurer le système d’ingestion des données à l’aide d’Azure Event Hubs et le connecter à Azure Databricks pour traiter les messages entrants. Pour accéder à un flux de données, vous allez utiliser des API Twitter pour faire ingérer des tweets à Event Hubs. Une fois les données reçues dans Azure Databricks, vous pourrez exécuter des tâches d’analyse pour aller plus loin.

À la fin de ce didacticiel, vous disposerez de tweets diffusés en continu de Twitter (comprenant le terme « Azure ») et lirez ces tweets dans Azure Databricks.

L’illustration suivante montre le flux d’application :

![Azure Databricks avec Event Hubs](./media/databricks-stream-from-eventhubs/databricks-eventhubs-tutorial.png "Azure Databricks avec Event Hubs")

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer une application Twitter pour accéder aux données de diffusion en continu
> * Créer des notebooks dans Azure Databricks
> * Joindre des bibliothèques pour Event Hubs et les API Twitter
> * Envoyer des tweets vers Event Hubs
> * Lire des tweets à partir d’Event Hubs

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!Note]
> Ce didacticiel ne peut pas être suivi avec un **abonnement d’essai gratuit Azure**.
> Si vous avez un compte gratuit, accédez à votre profil et modifiez votre abonnement sur **Paiement à l’utilisation**. Pour plus d’informations, consultez la page [Compte Azure gratuit](https://azure.microsoft.com/free/). Ensuite, [supprimez la limite de dépense](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), et [demandez une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour les processeurs virtuels dans votre région. Lorsque vous créez votre espace de travail Azure Databricks, vous pouvez sélectionner le tarif **Version d’évaluation (Premium - 14 jours de DBU offerts)** pour donner à l’accès de l’espace de travail un accès gratuit aux DBU d’Azure Databricks pendant 14 jours.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer le didacticiel, veillez à disposer des éléments suivants :
- Un espace de noms Azure Event Hubs.
- Un concentrateur d’événements au sein de l’espace de noms.
- Une chaîne de connexion pour accéder à l’espace de noms Event Hubs. La chaîne de connexion suit un format similaire à `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Un nom de stratégie d’accès partagé et une clé de stratégie pour Event Hubs.

Vous pouvez obtenir tous ces éléments en terminant les étapes de l’article [Créer un espace de noms et un concentrateur d’événements Azure Event Hubs](../event-hubs/event-hubs-create.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Dans cette section, vous créez un espace de travail Azure Databricks en utilisant le portail Azure.

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Données + Analytique** > **Azure Databricks**.

    ![Databricks sur le portail Azure](./media/databricks-stream-from-eventhubs/azure-databricks-on-portal.png "Databricks sur le portail Azure")

3. Sous **Service Azure Databricks**, renseignez les valeurs pour créer un espace de travail Databricks.

    ![Créer un espace de travail Azure Databricks](./media/databricks-stream-from-eventhubs/create-databricks-workspace.png "Créer un espace de travail Azure Databricks")

    Renseignez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |**Nom de l’espace de travail**     | Renseignez un nom pour votre espace de travail Databricks.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../azure-resource-manager/management/overview.md). |
    |**Lieu**     | Sélectionnez **USA Est**. Pour les autres régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).        |
    |**Niveau tarifaire**     |  Choisissez entre **Standard** ou **Premium**. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

4. La création du compte prend quelques minutes. Lors de la création d’un compte, le portail affiche la vignette **Envoi du déploiement pour Azure Databricks** sur le côté droit. Vous devrez peut-être faire défiler votre tableau de bord vers la droite pour voir la vignette. Il existe également une barre de progression en haut de l’écran. Vous pouvez surveiller la progression de la zone souhaitée.

    ![Vignette de déploiement Databricks](./media/databricks-stream-from-eventhubs/databricks-deployment-tile.png "Vignette de déploiement Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Créer un cluster Spark dans Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Cluster**.

    ![Databricks sur Azure](./media/databricks-stream-from-eventhubs/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/databricks-stream-from-eventhubs/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

    Acceptez toutes les valeurs par défaut autres que les suivantes :

   * Entrez un nom pour le cluster.
   * Pour cet article, créez un cluster avec le runtime **6.0**.
   * Veillez à cocher la case **Arrêter après \_\_ minutes d’inactivité**. Spécifiez une durée (en minutes) pour arrêter le cluster, si le cluster n’est pas utilisé.

   Sélectionnez la taille des nœuds worker et de pilote adaptée à vos critères techniques et à votre [budget](https://azure.microsoft.com/pricing/details/databricks/).

     Sélectionnez **Créer un cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

## <a name="create-a-twitter-application"></a>Création d'une application Twitter

Pour recevoir un flux de tweets, vous créez une application dans Twitter. Suivez les instructions pour créer une application Twitter et enregistrez les valeurs dont vous avez besoin pour terminer ce didacticiel.

1. Dans un navigateur web, accédez à [Twitter For Developers](https://developer.twitter.com/en/apps), puis sélectionnez **Create an app** (Créer une application). Vous voyez normalement un message indiquant que vous devez demander un compte de développeur Twitter. N’hésitez pas à le faire et, une fois votre application approuvée, vous devez voir un e-mail de confirmation. L’approbation d’un compte de développeur peut prendre plusieurs jours.

    ![Confirmation du compte de développeur Twitter](./media/databricks-stream-from-eventhubs/databricks-twitter-dev-confirmation.png "Confirmation du compte de développeur Twitter")

2. Sur la page **Créer une application**, renseignez les informations de la nouvelle application, puis sélectionnez **Créer votre application Twitter**.

    ![Détails de l’application Twitter](./media/databricks-stream-from-eventhubs/databricks-provide-twitter-app-details.png "Détails de l’application Twitter")

    ![Détails de l’application Twitter](./media/databricks-stream-from-eventhubs/databricks-provide-twitter-app-details-create.png "Détails de l’application Twitter")

3. Dans la page de l’application, sélectionnez l’onglet **Keys and Tokens** (Clés et jetons), et copiez les valeurs de **Consumer API Key** (Clé d’API du consommateur) et **Consumer API Secret Key** (Clé secrète d’API du consommateur). Sélectionnez aussi **Create** (Créer) sous **Access Token and Access Token Secret** (Jeton d’accès et secret du jeton d’accès) pour générer les jetons d’accès. Copiez les valeurs de **Jeton d’accès** et **Secret du jeton d’accès**.

    ![Détails de l’application Twitter](./media/databricks-stream-from-eventhubs/twitter-app-key-secret.png "Détails de l’application Twitter")

Enregistrez les valeurs que vous avez récupérées pour l’application Twitter. Vous aurez besoin de ces valeurs plus loin dans le didacticiel.

## <a name="attach-libraries-to-spark-cluster"></a>Joindre des bibliothèques au cluster Spark

Dans ce didacticiel, vous allez utiliser les API Twitter pour envoyer des tweets à Event Hubs. Vous allez aussi utiliser le [connecteur Apache Spark Event Hubs](https://github.com/Azure/azure-event-hubs-spark) pour lire et écrire des données dans Azure Event Hubs. Pour utiliser ces API au sein de votre cluster, ajoutez-les en tant que bibliothèques à Azure Databricks et associez-les à votre cluster Spark. Les instructions suivantes indiquent comment ajouter une bibliothèque.

1. Dans l’espace de travail Azure Databricks, sélectionnez **Clusters**, puis choisissez votre cluster Spark existant. Dans le menu du cluster, choisissez **Bibliothèques**, puis cliquez sur **Installer**.

   ![Boîte de dialogue Ajouter une bibliothèque](./media/databricks-stream-from-eventhubs/databricks-add-library-locate-cluster.png "Ajouter une bibliothèque - Localiser le cluster")

   ![Boîte de dialogue Ajouter une bibliothèque](./media/databricks-stream-from-eventhubs/databricks-add-library-install-new.png "Ajouter une bibliothèque - Installer")

2. Dans la page Nouvelle bibliothèque, pour **Source**, sélectionnez **Maven**. Entrez séparément les coordonnées suivantes pour le connecteur Spark Event Hubs et l’API Twitter dans **Coordonnées**.

   * Connecteur Spark Event Hubs : `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.12`
   * API Twitter : `org.twitter4j:twitter4j-core:4.0.7`

3. Sélectionnez **Installer**.

4. Dans le menu du cluster, vérifiez que les deux bibliothèques sont installées et attachées correctement.

    ![Vérifier les bibliothèques](./media/databricks-stream-from-eventhubs/databricks-add-library-check.png "Vérifier les bibliothèques")

6. Répétez ces étapes pour le package Twitter, `twitter4j-core:4.0.7`.

## <a name="create-notebooks-in-databricks"></a>Créer des notebooks dans Databricks

Dans cette section, vous allez créer deux notebooks dans l’espace de travail Databricks avec les noms suivants :

- **SendTweetsToEventHub** : un notebook producteur à utiliser pour obtenir des tweets de Twitter et les diffuser dans Event Hubs.
- **ReadTweetsFromEventHub** : un notebook consommateur à utiliser pour lire les tweets à partir d’Event Hubs.

1. Dans le volet gauche, sélectionnez **Espace de travail**. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** > **Notebook**.

    ![Créer un notebook dans Databricks](./media/databricks-stream-from-eventhubs/databricks-create-notebook.png "Créer un notebook dans Databricks")

2. Dans la boîte de dialogue **Créer un notebook**, entrez **SendTweetsToEventHub**, sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Créer un notebook dans Databricks](./media/databricks-stream-from-eventhubs/databricks-notebook-details.png "Créer un notebook dans Databricks")

    Sélectionnez **Create** (Créer).

3. Répétez les étapes pour créer le notebook **ReadTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Envoyer des tweets vers Event Hubs

Dans le notebook **SendTweetsToEventHub**, collez le code suivant, puis remplacez les espaces réservés par les valeurs de votre espace de noms Event Hubs et de l’application Twitter que vous avez créés précédemment. Ce notebook diffuse les tweets avec le mot-clé « Azure » dans Event Hubs en temps réel.

> [!NOTE]
> L’API Twitter a certaines restrictions et [quotas](https://developer.twitter.com/en/docs/basics/rate-limiting.html) pour les demandes. Si vous n’êtes pas satisfait de la limitation du débit standard dans l’API Twitter, vous pouvez générer du contenu texte sans utiliser l’API Twitter dans cet exemple. Pour cela, définissez la variable **dataSource** sur `test` au lieu de `twitter` et renseignez la liste **testSource** avec l’entrée de test par défaut.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
      val twitterOauthAccessToken = "<ACCESS TOKEN>"
      val twitterOauthTokenSecret = "<TOKEN SECRET>"

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
      while (!finished) {
        val result = twitter.search(query)
        val statuses = result.getTweets()
        var lowestStatusId = Long.MaxValue
        for (status <- statuses.asScala) {
          if(!status.isRetweet()){
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

Appuyez sur **Maj+Entrée** pour exécuter le notebook. Vous verrez une sortie identique à l’extrait de code ci-dessous. Chaque événement de la sortie représente un tweet ingéré par Event Hubs contenant le terme « Azure ».

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Lire des tweets à partir d’Event Hubs

Dans le notebook **ReadTweetsFromEventHub**, collez le code suivant, et remplacez l’espace réservé par les valeurs du concentrateur d’événements Azure Event Hubs que vous avez créé précédemment. Ce notebook lit les tweets que vous avez diffusés précédemment dans Event Hubs à l’aide du notebook **SendTweetsToEventHub**.

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Vous obtenez la sortie suivante :


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Étant donné que la sortie est en mode binaire, utilisez l’extrait de code suivant pour la convertir en chaîne.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

La sortie ressemble maintenant à l’extrait de code suivant :

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Et voilà ! Avec Azure Databricks, vous avez réussi à diffuser en continu des données dans Azure Event Hubs en temps quasi-réel. Vous avez ensuite utilisé les données de flux avec le connecteur Event Hubs pour Apache Spark. Pour plus d’informations sur la façon d’utiliser le connecteur Event Hubs pour Spark, consultez la [documentation relative au connecteur](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois le tutoriel terminé, vous pouvez arrêter le cluster. Pour cela, dans l’espace de travail Azure Databricks, dans le volet gauche, sélectionnez **Clusters**. Pour le cluster que vous voulez arrêter, déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis sélectionnez l’icône **Arrêter**.

![Arrêter un cluster Databricks](./media/databricks-stream-from-eventhubs/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous n’arrêtez pas le cluster manuellement, il s’arrête automatiquement, à condition d’avoir coché la case **Arrêter après \_\_ minutes d’inactivité** durant la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer une application Twitter pour générer des données de diffusion en continu
> * Créer des notebooks dans Azure Databricks
> * Ajouter des bibliothèques pour Event Hubs et les API Twitter
> * Envoyer des tweets vers Event Hubs
> * Lire des tweets à partir d’Event Hubs

Passez au tutoriel suivant pour découvrir comment exécuter une analyse des sentiments sur les données diffusées en streaming avec Azure Databricks et l’[API Cognitive Services](../cognitive-services/text-analytics/overview.md).

> [!div class="nextstepaction"]
>[Analyse des sentiments sur des données de streaming à l’aide d’Azure Databricks](databricks-sentiment-analysis-cognitive-services.md)
