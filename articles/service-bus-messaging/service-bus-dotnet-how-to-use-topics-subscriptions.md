---
title: Envoyer des messages à des rubriques Azure Service Bus à l’aide d’azure-messaging-servicebus
description: Ce guide de démarrage rapide vous montre comment envoyer des messages à des rubriques Azure Service Bus à l’aide du package azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279797"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Envoyer des messages à une rubrique Azure Service Bus et recevoir des messages à partir des abonnements à cette rubrique (.NET)
Dans ce tutoriel, vous créez une application C# pour effectuer les tâches suivantes :

1. Envoyer des messages à une rubrique Service Bus. 

    Une rubrique Service Bus fournit un point de terminaison pour les applications d’expéditeur afin d’envoyer des messages. Une rubrique peut avoir un ou plusieurs abonnements. Chaque abonnement d’une rubrique obtient une copie du message envoyé à la rubrique. Pour en savoir plus sur la tarification Service Bus, consultez [Qu’est-ce que Service Bus ?](service-bus-messaging-overview.md). 
1. Recevez des messages d’un abonnement à la rubrique. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Rubriques et abonnements Service Bus":::

    > [!Important]
    > Ce guide de démarrage rapide utilise le nouveau package **Azure.Messaging.ServiceBus**. Si vous utilisez l’ancien package Microsoft.Azure.ServiceBus, consultez [Envoyer et recevoir des messages à l’aide du package Microsoft.Azure.ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez activer les [avantages de votre abonnement Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou vous inscrire pour créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Suivez les étapes de [Démarrage rapide](service-bus-quickstart-topics-subscriptions-portal.md) pour créer une rubrique Service Bus et des abonnements à cette rubrique. 

    > [!NOTE]
    > Dans ce didacticiel, vous allez utiliser la chaîne de connexion à l’espace de noms, le nom de la rubrique et le nom de l’un des abonnements à la rubrique.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique
Dans cette section, vous allez créer une application console .NET Core dans Visual Studio et ajouter un code pour envoyer des messages à la rubrique Service Bus que vous avez créée. 

### <a name="create-a-console-application"></a>Création d’une application console
Créez une application console .NET Core avec Visual Studio. 

1. Lancez Visual Studio.  
1. Si la page **Démarrage** s’affiche, sélectionnez **Créer un nouveau projet**. 
1. Sur la page **Créer un nouveau projet**, procédez comme suit : 
    1. Pour le langage de programmation, sélectionnez **C#** . 
    1. Pour le type de projet, sélectionnez **Console**. 
    1. Dans la liste des modèles, sélectionnez **Application console (.NET Core)** . 
    1. Ensuite, sélectionnez **Suivant**. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Créer un projet d’application console":::
1. Sur la page **Configurer votre nouveau projet**, procédez comme suit : 
    1. Pour **Nom du projet**, entrez un nom pour le projet. 
    1. Pour **Emplacement**, sélectionnez un emplacement pour les fichiers projet et solution. 
    1. Pour **Nom de la solution**, entrez un nom pour la solution. Une solution Visual Studio peut contenir un ou plusieurs projets. Dans ce démarrage rapide, la solution n’aura qu’un seul projet. 
    1. Sélectionnez **Créer** pour créer le projet. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Entrer les noms et les emplacements du projet et de la solution":::    


### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus
1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="Gérer le menu des packages NuGet":::        
1. Basculez vers l’onglet **Parcourir**.
1. Recherchez et sélectionnez **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Sélectionnez **Installer** pour terminer l’installation.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Sélectionnez package NuGet Service Bus.":::
5. Si la boîte de dialogue **Aperçu des modifications** s’affiche, sélectionnez **OK** pour continuer. 
1. Si la page **Acceptation de la licence** s’affiche, sélectionnez **J’accepte** pour continuer. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Ajouter du code pour envoyer des messages à la rubrique 

1. Dans la fenêtre **Explorateur de solutions**, double-cliquez sur **Program.cs** pour ouvrir le fichier dans l’éditeur. 
1. Ajoutez les instructions `using` suivantes en haut de la définition de l’espace de noms, avant la déclaration de classe :
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Dans la classe `Program`, au-dessus de la fonction `Main`, déclarez les variables suivantes :

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Remplacez les valeurs suivantes :
    - `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion de votre espace de noms Service Bus
    - `<TOPIC NAME>` par le nom de la rubrique
    - `<SUBSCRIPTION NAME>` par le nom de l’abonnement

### <a name="send-a-single-message-to-the-topic"></a>Envoyer un message unique à la rubrique
Ajoutez une méthode nommée `SendMessageToTopicAsync` à la classe `Program` au-dessus ou en dessous de la méthode `Main`. Cette méthode permet d’envoyer un message unique à la rubrique.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

Cette méthode effectue les étapes suivantes : 
1. Elle crée un objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) à l’aide de la chaîne de connexion à l’espace de noms. 
1. Elle utilise l'objet `ServiceBusClient` pour créer un objet [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) pour la rubrique Service Bus spécifiée. Cette étape utilise la méthode [ServiceBusClient.CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender).
1. Ensuite, la méthode envoie un seul message test à la rubrique Service Bus avec la méthode [ServiceBusSender.SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) . 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Envoyer un lot de messages à la rubrique
1. Ajoutez une méthode nommée `CreateMessages` pour créer une file d’attente (file d’attente .NET, et non une file d'attente Service Bus) de messages à la classe `Program`. En général, vous obtenez ces messages à partir de différentes parties de votre application. Ici, nous créons une file d’attente d’exemples de messages. Si vous ne connaissez pas les files d’attente .NET, consultez [Queue.Enqueue](/dotnet/api/system.collections.queue.enqueue).

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Ajoutez une méthode nommée `SendMessageBatchAsync` à la classe `Program` et ajoutez le code suivant. Cette méthode prend une file d’attente de messages et prépare un ou plusieurs lots à envoyer à la rubrique Service Bus. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    Voici les étapes importantes du code :
    1. Crée un objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) à l’aide de la chaîne de connexion à l’espace de noms. 
    1. Utilise la méthode [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) sur l’objet `ServiceBusClient` pour créer un objet [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) pour la rubrique Service Bus spécifiée. 
    1. Appelle la méthode d’assistance `GetMessages` pour obtenir une file d’attente de messages à envoyer à la rubrique Service Bus. 
    1. Crée un [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) à l’aide de [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Ajoutez des messages au lot à l’aide de [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). À mesure que les messages sont ajoutés au lot, ils sont supprimés de la file d’attente .NET. 
    1. Envoie le lot de messages à la rubrique Service Bus avec la méthode [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync).

### <a name="update-the-main-method"></a>Mettre à jour la méthode principale
Remplacez la méthode `Main()` par la méthode **asynchrone** `Main` suivante. Elle appelle ces deux méthodes Send pour envoyer un message unique et un lot de messages à la rubrique.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Tester l’application pour envoyer des messages à la rubrique
1. Exécutez l'application. Vous devez normalement voir la sortie suivante :

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Dans le portail Azure, procédez comme suit :
    1. Accédez à votre espace de noms Service Bus. 
    1. Dans la page **Vue d’ensemble**, dans le volet central inférieur, basculez vers l’onglet **Rubriques**, puis sélectionnez la rubrique Service Bus. Dans l’exemple suivant, il s’agit de `mytopic`.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Sélectionner la rubrique":::
    1. Dans la page **Rubrique Service Bus**, dans le graphique **Messages** de la section inférieure **Métriques**, vous pouvez voir quatre messages entrants pour la rubrique. Si vous ne voyez pas cette valeur, attendez quelques minutes et actualisez la page pour voir une mise à jour du graphique. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Messages envoyés à la rubrique" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Sélectionnez l’abonnement dans le volet inférieur. Dans l’exemple suivant, il s’agit de **S1**. Dans la page **Abonnement Service Bus**, vous voyez que **Nombre de messages actifs** a pour valeur **4**. L’abonnement a reçu les quatre messages que vous avez envoyés à la rubrique, mais aucun destinataire ne les a encore collectés. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Messages reçus au niveau de l’abonnement" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement

1. Ajoutez les méthodes suivantes à la classe `Program` pour traiter les messages et les erreurs éventuelles. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Ajoutez la méthode `ReceiveMessagesFromSubscriptionAsync` suivante à la classe `Program`.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```

    Voici les étapes importantes du code :
    1. Crée un objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) à l’aide de la chaîne de connexion à l’espace de noms. 
    1. Appelle la méthode [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) sur l'objet `ServiceBusClient` pour créer un objet [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) pour la rubrique de Service Bus spécifiée et la combinaison d’abonnements. 
    1. Spécifie des gestionnaires pour les événements [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) et [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) de l’objet `ServiceBusProcessor`. 
    1. Démarre le traitement des messages en appelant [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) sur l' objet `ServiceBusProcessor`. 
    1. Lorsque l’utilisateur appuie sur une clé pour terminer le traitement, il appelle [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) sur l' objet `ServiceBusProcessor`. 
1. Ajoutez un appel à la méthode `ReceiveMessagesFromSubscriptionAsync` dans la méthode `Main`. Mettez en commentaire la méthode `SendMessagesToTopicAsync` si vous souhaitez tester uniquement la réception des messages. Dans le cas contraire, vous voyez quatre autres messages envoyés à la rubrique. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Exécuter l’application
Exécutez l'application. Attendez une minute, puis appuyez sur une touche quelconque pour cesser de recevoir des messages. Vous devriez voir la sortie suivante (barre d’espace pour la touche). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Vérifiez à nouveau le portail. 

- Dans la page **Rubrique Service Bus**, dans le graphique **Messages**, vous voyez huit messages entrants et huit messages sortants. Si vous ne voyez pas ces valeurs, attendez quelques minutes et actualisez la page pour voir une mise à jour du graphique. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Messages envoyés et reçus" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Dans la page **Abonnement Service Bus**, vous voyez que **Nombre de messages actifs** est égal à zéro. Cela est dû au fait qu’un destinataire a reçu des messages de cet abonnement et a terminé les messages. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Nombre de messages actifs au niveau de l’abonnement à la fin" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation et les exemples suivants :

- [Bibliothèque de client Azure Service Bus pour .NET – Fichier Lisez-moi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Échantillons .NET pour Azure Service Bus sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Informations de référence sur l’API .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true)