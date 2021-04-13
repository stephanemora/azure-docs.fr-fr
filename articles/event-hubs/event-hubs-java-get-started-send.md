---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant Java (dernière version)
description: Cet article décrit la procédure à suivre pour créer une application Java qui reçoit des événements d’Azure Event Hubs et lui en envoie à l’aide du dernier package azure-messaging-eventhubs.
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448438"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Utiliser Java pour recevoir des événements d’Azure Event Hubs ou lui en envoyer (azure-messaging-eventhubs)
Ce guide de démarrage rapide montre comment recevoir des événements d’un hub d’événements et lui en envoyer en utilisant le package Java **azure-messaging-eventhubs**.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise le nouveau package **azure-messaging-eventhubs**. Pour un guide de démarrage rapide qui utilise les anciens packages **azure-eventhubs** et **azure-eventhubs-eph**, consultez [Envoyer et recevoir des événements à l’aide d’azure-eventhubs et azure-eventhubs-eph](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Prérequis
Si vous débutez avec Azure Event Hubs, consultez la [vue d’ensemble d’Event Hubs](event-hubs-about.md) avant de suivre ce guide de démarrage rapide. 

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- **Abonnement Microsoft Azure**. Pour utiliser les services Azure, y compris Azure Event Hubs, vous avez besoin d’un abonnement.  Si vous n’avez pas de compte Azure, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/) ou utiliser les avantages de votre abonnement MSDN quand vous [créez un compte](https://azure.microsoft.com).
- Un environnement de développement Java. Ce guide de démarrage rapide utilise [Eclipse](https://www.eclipse.org/). Le Kit de développement Java (JDK) avec version 8 ou ultérieure est nécessaire. 
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md). Ensuite, obtenez la **chaîne de connexion de l’espace de noms Event Hubs** en suivant les instructions à partir de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce guide de démarrage rapide.

## <a name="send-events"></a>Envoyer des événements 
Cette section montre comment créer une application Java pour envoyer des événements à un hub d’événements. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Ajouter une référence à la bibliothèque Azure Event Hubs

La bibliothèque cliente de Java pour Event Hubs est disponible dans le [Référentiel central Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Vous pouvez référencer cette bibliothèque à l’aide de la déclaration de dépendance suivante au sein de votre fichier de projet Maven :

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> Procédez à une mise à jour vers la dernière version publiée sur le référentiel Maven. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un hub d’événements

Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. Ajoutez une classe nommée `Sender`, et ajoutez-y le code suivant :

> [!IMPORTANT]
> Remplacez `<Event Hubs namespace connection string>` par la chaîne de connexion de votre espace de noms Event Hubs. Remplacez `<Event hub name>` par le nom de votre instance Event Hub dans l'espace de noms. 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>Ajouter du code pour publier des événements dans l'instance Event Hub
Ajoutez une méthode nommée `publishEvents` à la classe `Sender`, comme indiqué ci-dessous : 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

Générez le programme et assurez-vous qu’il n’y a aucune erreur. Vous exécuterez ce programme après avoir exécuté le programme récepteur. 

## <a name="receive-events"></a>Recevoir des événements
Le code de ce tutoriel est basé sur l’[exemple EventProcessorClient sur GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java), que vous pouvez analyser pour voir la version complète de l’application en cours.

> [!WARNING]
> Si vous exécutez ce code sur Azure Stack Hub, vous rencontrerez des erreurs d’exécution, sauf si vous ciblez une version spécifique de l’API Stockage. Ceci est dû au fait que le SDK Event Hubs utilise la dernière API Stockage Azure disponible dans Azure, qui peut ne pas être pas disponible sur votre plateforme Azure Stack Hub. Azure Stack Hub peut prendre en charge une autre version du SDK Stockage Blob que celles généralement disponibles sur Azure. Si vous utilisez Stockage Blob Azure comme magasin de points de contrôle, vérifiez la [version de l’API Stockage Azure prise en charge pour votre build Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) et ciblez cette version dans votre code. 
>
> Par exemple, si vous exécutez sur Azure Stack Hub version 2005, la version la plus élevée disponible pour le service Stockage est la version 2019-02-02. Par défaut, la bibliothèque de client du SDK Event Hubs utilise la version la plus récente disponible sur Azure (2019-07-07 au moment de la publication du SDK). Dans ce cas, en plus des étapes suivantes de cette section, vous devrez également ajouter du code pour cibler la version 2019-02-02 de l’API du service Stockage. Pour obtenir un exemple sur la façon de cibler une version spécifique de l’API de stockage, consultez [cet exemple sur GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Créer un compte Stockage Azure et un conteneur de blobs
Dans ce guide de démarrage rapide, vous utilisez Stockage Azure (plus particulièrement, Stockage Blob) comme magasin de points de contrôle. Le marquage de point de contrôle est un processus par lequel un processeur d’événements marque ou valide la position du dernier événement correctement traité dans une partition. Le marquage d’un point de contrôle s’effectue généralement au sein de la fonction qui traite les événements. Pour en savoir plus sur le marquage de point de contrôle, consultez [Processeur d’événements](event-processor-balance-partition-load.md).

Suivez les étapes ci-dessous pour créer un compte Stockage Azure. 

1. [Création d’un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. Créer un [conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenir la chaîne de connexion au compte de stockage](../storage/common/storage-configure-connection-string.md)

    Notez la **chaîne de connexion** et le **nom du conteneur**. Vous les utiliserez dans le code de réception. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Ajouter des bibliothèques Event Hubs à votre projet Java
Ajoutez les dépendances suivantes dans le fichier pom.xml. 

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. Ajoutez les instructions **import** suivantes au début du fichier Java. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. Créez une classe nommée `Receiver`, puis ajoutez les variables de chaîne suivantes à la classe. Remplacez les espaces réservés par les valeurs correctes. 

    > [!IMPORTANT]
    > Remplacez les espaces réservés par les valeurs correctes.
    > - `<Event Hubs namespace connection string>` par la chaîne de connexion de votre espace de noms Event Hubs. Update 
    > - `<Event hub name>` par le nom de votre instance Event Hub dans l'espace de noms. 
    > - `<Storage connection string>` par la chaîne de connexion de votre compte de stockage Azure. 
    > - `<Storage container name>` par le nom de votre conteneur dans votre stockage d'objets blob Azure. 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. Ajoutez la méthode `main` suivante à la classe. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Ajoutez les deux méthodes d’assistance (`PARTITION_PROCESSOR` et `ERROR_HANDLER`) qui traitent les événements et les erreurs à la classe `Receiver`. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Générez le programme et assurez-vous qu’il n’y a aucune erreur. 

## <a name="run-the-applications"></a>Exécution des applications
1. Exécutez d’abord l’application **réceptrice**.
1. Ensuite, exécutez l’application **émettrice**. 
1. Dans la fenêtre d’application **réceptrice**, vérifiez que vous voyez les événements qui ont été publiés par l’application émettrice.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. Appuyez sur **Entrée** dans la fenêtre d’application réceptrice pour arrêter l’application. 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>Étapes suivantes
Consultez les exemples suivants sur GitHub :

- [Exemples azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Exemples azure-messaging-eventhubs-checkpointstore-blob](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob)  
