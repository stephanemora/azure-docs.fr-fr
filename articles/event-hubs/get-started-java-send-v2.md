---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant Java (dernière version)
description: Cet article décrit la procédure à suivre pour créer une application Java qui reçoit des événements d’Azure Event Hubs et lui en envoie à l’aide du dernier package azure-messaging-eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: d9d22374868f3befd659918c532f339d49ba1642
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032047"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Utiliser Java pour recevoir des événements d’Azure Event Hubs ou lui en envoyer (azure-messaging-eventhubs)
Ce guide de démarrage rapide montre comment créer des applications Java pour recevoir des événements d’Azure Event Hubs ou lui en envoyer. 

Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise le nouveau package **azure-messaging-eventhubs**. Pour un guide de démarrage rapide qui utilise les anciens packages **azure-eventhubs** et **azure-eventhubs-eph**, consultez [cet article](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Un environnement de développement Java. Ce tutoriel utilise [Fiddler](https://www.eclipse.org/). Le Kit de développement Java (JDK) avec version 8 ou ultérieure est nécessaire. 
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md). Ensuite, obtenez la valeur de la clé d’accès du hub d’événements en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utilisez la clé d’accès dans le code que vous écrivez plus loin dans ce tutoriel. Le nom de clé par défaut est : **RootManageSharedAccessKey**.

## <a name="send-events"></a>Envoyer des événements 
Cette section montre comment créer une application Java pour envoyer des événements à un hub d’événements. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Ajouter une référence à la bibliothèque Azure Event Hubs

La bibliothèque cliente de Java pour les concentrateurs d’événements peut être utilisée dans les projets Maven à partir du [référentiel central Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Vous pouvez référencer cette bibliothèque à l’aide de la déclaration de dépendance suivante au sein de votre fichier de projet Maven :

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un hub d’événements

Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. Ajoutez une classe nommée `SimpleSend`, et ajoutez-y le code suivant :

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
    public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Chaîne de connexion et hub d’événements
Ce code utilise la chaîne de connexion à l’espace de noms Event Hubs et le nom du hub d’événements pour créer un client Event Hubs. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Créer un client producteur Event Hubs 
Ce code crée un objet client producteur utilisé pour produire/envoyer des événements au hub d’événements. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducer();
```

### <a name="prepare-a-batch-of-events"></a>Préparer un lot d’événements
Ce code prépare un lot d’événements. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Envoyer le lot d’événements au hub d’événements
Ce code envoie au hub d’événements le lot d’événements que vous avez préparé à l’étape précédente. Le code suivant garde la main jusqu’à la fin de l’opération d’envoi. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Fermer et nettoyer
Ce code ferme le producteur. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Code complet pour envoyer des événements
Voici le code complet pour envoyer des événements au hub d’événements. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducer();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Générez le programme et assurez-vous qu’il n’y a aucune erreur. Vous exécuterez ce programme après avoir exécuté le programme récepteur. 

## <a name="receive-events"></a>Recevoir des événements
Le code de ce tutoriel est basé sur l’[exemple EventProcessorClient sur GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), que vous pouvez analyser pour voir la version complète de l’application en cours.

### <a name="create-a-java-project"></a>Créer un projet Java

La bibliothèque cliente Java pour Event Hubs est utilisable dans les projets Maven à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22). Elle peut être référencée à l’aide de la déclaration de dépendance ci-après dans votre fichier projet Maven : 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Utilisez le code suivant pour créer une classe appelée `Receiver`. Remplacez les espaces réservés par les valeurs utilisées lorsque vous avez créé le concentrateur d’événements et le compte de stockage :
   
   ```java
    import com.azure.messaging.eventhubs.*;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;

    public class Receiver {

        private static final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static final String eventHubName = "EVENT HUB NAME";
    
        public static void main(String[] args) throws Exception {

            // function to process events
            Consumer<EventContext> processEvent = eventContext  -> {
                System.out.print("Received event: ");
                // print the body of the event
                System.out.println(eventContext.getEventData().getBodyAsString());
                eventContext.updateCheckpoint();
            };

            // function to process errors
            Consumer<ErrorContext> processError = errorContext -> {
                // print the error message
                System.out.println(errorContext.getThrowable().getMessage());
            };

            EventProcessorBuilder eventProcessorBuilder = new EventProcessorBuilder()
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .connectionString(connectionString, eventHubName)
                .processEvent(processEvent)
                .processError(processError)
                .checkpointStore(new InMemoryCheckpointStore());
        
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();
            System.out.println("Starting event processor");
            eventProcessorClient.start();

            System.out.println("Press enter to stop.");
            System.in.read();

            System.out.println("Stopping event processor");
            eventProcessor.stop();
            System.out.println("Event processor stopped.");
    
            System.out.println("Exiting process");
        }
    }
    ```
    
2. Téléchargez le fichier **InMemoryCheckpointStore.java** à partir de [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs), puis ajoutez-le à votre projet. 
3. Générez le programme et assurez-vous qu’il n’y a aucune erreur. 

## <a name="run-the-applications"></a>Exécution des applications
1. Exécutez d’abord l’application **réceptrice**.
1. Ensuite, exécutez l’application **émettrice**. 
1. Dans la fenêtre d’application **réceptrice**, vérifiez que vous voyez les événements qui ont été publiés par l’application émettrice.
1. Appuyez sur **Entrée** dans la fenêtre d’application réceptrice pour arrêter l’application. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les [exemples du SDK Java sur GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

