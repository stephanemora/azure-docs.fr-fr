---
title: Utiliser des rubriques et abonnements Azure Service Bus avec Java (azure-messaging-servicebus)
description: Dans ce guide de démarrage rapide, vous allez écrire du code Java à l’aide du package azure-messaging-servicebus pour envoyer des messages à une rubrique Azure Service Bus, puis vous recevrez les messages des abonnements à cette rubrique.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.openlocfilehash: c5b930fb2c87a09a1f4801365936c62a7cf79f1d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516173"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Envoyer des messages à une rubrique Azure Service Bus et recevoir des messages à partir d’abonnements à la rubrique (Java)
Dans ce guide de démarrage rapide, vous allez écrire du code Java à l’aide du package azure-messaging-servicebus pour envoyer des messages à une rubrique Azure Service Bus, puis vous recevrez les messages des abonnements à cette rubrique.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise le nouveau package azure-messaging-servicebus. Pour un guide de démarrage rapide qui utilise l’ancien package azure-servicebus, consultez [Envoyer et recevoir des messages à l’aide d’azure-servicebus](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez activer les [avantages de votre abonnement Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou vous inscrire pour créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Suivez les étapes dans [Démarrage rapide : Utiliser le portail Azure pour créer une rubrique Service Bus et des abonnements à cette rubrique](service-bus-quickstart-topics-subscriptions-portal.md). Notez la chaîne de connexion, le nom de la rubrique et un nom d’abonnement. Vous n’utiliserez qu’un seul abonnement dans ce guide de démarrage rapide. 
- Installez le [kit de développement logiciel (SDK) Azure pour Java][Azure SDK for Java]. Si vous utilisez Eclipse, vous pouvez installer le [Kit de ressources Azure pour Eclipse][Azure Toolkit for Eclipse] qui inclut le kit SDK Azure pour Java. Vous pouvez ensuite ajouter les **bibliothèques Microsoft Azure pour Java** à votre projet. Si vous utilisez IntelliJ, consultez [Installer Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique
Dans cette section, vous allez créer un projet de console Java et ajouter du code pour envoyer des messages à la rubrique que vous avez créée. 

### <a name="create-a-java-console-project"></a>Créer un projet de console Java
Créez un projet Java avec Eclipse ou un outil de votre choix. 

### <a name="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l’utilisation de Service Bus
Ajoutez des références aux bibliothèques Azure Core et Azure Service Bus. 

Si vous utilisez Eclipse et que vous avez créé une application console Java, convertissez votre projet Java en Maven : cliquez avec le bouton droit sur le projet dans la fenêtre **Explorateur de package**, puis sélectionnez **Configurer** -> **Convertir en projet Maven**. Ajoutez ensuite des dépendances à ces deux bibliothèques, comme indiqué dans l’exemple suivant.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Ajouter du code pour envoyer des messages à la rubrique
1. Ajoutez les instructions `import` suivantes au début du fichier Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Dans la classe, définissez des variables pour stocker la chaîne de connexion et le nom de la rubrique, comme indiqué ci-dessous : 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Remplacez `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion de votre espace de noms Service Bus, et remplacez `<TOPIC NAME>` par le nom de la rubrique.
3. Ajoutez une méthode nommée `sendMessage` dans la classe pour envoyer un message à la rubrique. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. Ajoutez une méthode nommée `createMessages` dans la classe pour créer une liste de messages. En général, vous recevez ces messages à partir de différentes parties de votre application. Ici, nous créons une liste d’exemples de messages.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Ajoutez une méthode nommée `sendMessageBatch` pour envoyer des messages à la rubrique que vous avez créée. Cette méthode crée un `ServiceBusSenderClient` pour la rubrique, appelle la méthode `createMessages` pour obtenir la liste des messages, prépare un ou plusieurs lots, et envoie les lots à la rubrique. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement
Dans cette section, vous allez ajouter du code pour récupérer les messages à partir d’un abonnement à la rubrique. 

1. Ajoutez une méthode nommée `receiveMessages` pour recevoir des messages à partir de l’abonnement. Cette méthode crée un `ServiceBusProcessorClient` pour l’abonnement en spécifiant un gestionnaire pour le traitement des messages et un autre pour la gestion des erreurs. Ensuite, elle démarre le processeur, attend quelques secondes, imprime les messages reçus, puis arrête et ferme le processeur.

    > [!IMPORTANT]
    > Dans le code, remplacez `ServiceBusTopicTest` dans `ServiceBusTopicTest::processMessage` par le nom de votre classe. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }  
    ```
2. Ajoutez la méthode `processMessage` pour traiter un message reçu de l’abonnement Service Bus. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Ajoutez la méthode `processError` pour gérer les messages d’erreur.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
1. Mettez à jour la méthode `main` pour appeler les méthodes `sendMessage`, `sendMessageBatch`et `receiveMessages`, et pour lever `InterruptedException`.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Exécuter l’application
Exécutez le programme pour obtenir une sortie de ce type :

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

Dans la page **Vue d’ensemble** de l’espace de noms Service Bus dans le portail Azure, vous pouvez voir le nombre de messages **entrants** et **sortants**. Vous devrez peut-être attendre environ une minute puis actualiser la page pour voir les valeurs les plus récentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Nombre de messages entrants et sortants" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Basculez vers l’onglet **Rubriques** dans le volet du milieu inférieur, puis sélectionnez la rubrique pour afficher la page **Rubrique Service Bus** correspondant à votre rubrique. Dans cette page, vous devez voir quatre messages entrants et quatre messages sortants dans le graphique **Messages**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Messages entrants et sortants" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Si vous commentez l’appel `receiveMessages` dans la méthode `main` et que vous réexécutez l’application, dans la page **Rubrique Service Bus** vous verrez huit messages entrants (quatre nouveaux) mais quatre messages sortants. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Page de rubrique mise à jour" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Dans cette page, si vous sélectionnez un abonnement, vous accédez à la page **Abonnement Service Bus**. Elle indique entre autres le nombre de messages actifs et le nombre de messages de lettres mortes. Dans cet exemple, quatre messages actifs n’ont pas encore été reçus par un récepteur. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Nombre de messages actifs" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Étapes suivantes
Voir la documentation et les exemples suivants :

- [Bibliothèque de client Azure Service Bus pour Java - Lisez-moi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exemples sur GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Informations de référence sur l’API Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage