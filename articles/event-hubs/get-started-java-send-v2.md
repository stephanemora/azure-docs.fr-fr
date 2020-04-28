---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant Java (dernière version)
description: Cet article décrit la procédure à suivre pour créer une application Java qui reçoit des événements d’Azure Event Hubs et lui en envoie à l’aide du dernier package azure-messaging-eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: spelluru
ms.openlocfilehash: ca22f4481750abb3bd4432c8b42fbce93ede8ffd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770876"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Utiliser Java pour recevoir des événements d’Azure Event Hubs ou lui en envoyer (azure-messaging-eventhubs)
Ce guide de démarrage rapide montre comment recevoir des événements d’un hub d’événements et lui en envoyer en utilisant le package Java **azure-messaging-eventhubs**.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise le nouveau package **azure-messaging-eventhubs**. Pour un guide de démarrage rapide qui utilise les anciens packages **azure-eventhubs** et **azure-eventhubs-eph**, consultez [Envoyer et recevoir des événements à l’aide d’azure-eventhubs et azure-eventhubs-eph](event-hubs-java-get-started-send.md). 


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
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un hub d’événements

Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. Ajoutez une classe nommée `Sender`, et ajoutez-y le code suivant :

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
    .buildProducerClient();
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
            .buildProducerClient();

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

> [!NOTE]
> Si vous exécutez sur Azure Stack Hub, cette plateforme peut prendre en charge une autre version du kit SDK Storage Blob que celles généralement disponibles sur Azure. Par exemple, si vous exécutez [sur Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), la version la plus élevée disponible pour le service Stockage est la version 2017-11-09. Dans ce cas, outre les étapes suivantes de cette section, vous devrez également ajouter du code pour cibler la version 2017-11-09 de l’API du service Stockage. Pour obtenir un exemple sur la façon de cibler une version spécifique de l’API de stockage, consultez [cet exemple sur GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Pour plus d’informations sur les versions du service Stockage Azure prises en charge sur Azure Stack Hub, reportez-vous à [Stockage Azure Stack Hub : différences et considérations](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

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
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
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
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new SampleCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. Téléchargez le fichier **SampleCheckpointStore.java** à partir de [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java), puis ajoutez-le à votre projet. 
3. Générez le programme et assurez-vous qu’il n’y a aucune erreur. 

## <a name="run-the-applications"></a>Exécution des applications
1. Exécutez d’abord l’application **réceptrice**.
1. Ensuite, exécutez l’application **émettrice**. 
1. Dans la fenêtre d’application **réceptrice**, vérifiez que vous voyez les événements qui ont été publiés par l’application émettrice.
1. Appuyez sur **Entrée** dans la fenêtre d’application réceptrice pour arrêter l’application. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les [exemples du SDK Java sur GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

