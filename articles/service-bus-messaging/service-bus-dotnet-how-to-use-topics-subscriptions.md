---
title: Prise en main des rubriques et abonnements Azure Service Bus
description: Ce guide de démarrage rapide vous montre comment envoyer des messages à des rubriques Azure Service Bus à l’aide du package azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 06/29/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: b101603577324200c348a7522596b44db8049a05
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457840"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-its-subscriptions-net"></a>Envoyer des messages à une rubrique Azure Service Bus et recevoir des messages de ses abonnements (.NET)
Ce démarrage rapide montre comment envoyer des messages à une rubrique Service Bus et recevoir des messages d’un abonnement à cette rubrique à l’aide de la bibliothèque .NET [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/).

## <a name="prerequisites"></a>Prérequis
Si vous débutez avec le service, consultez [Vue d’ensemble de Service Bus](service-bus-messaging-overview.md) avant de suivre ce démarrage rapide. 

- **Abonnement Azure**. Pour utiliser des services Azure, dont Azure Service Bus, vous avez besoin d’un abonnement.  Si vous n’avez pas de compte Azure, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/) ou utiliser les avantages de votre abonnement MSDN quand vous [créez un compte](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. La bibliothèque cliente Azure Service Bus utilise les nouvelles fonctionnalités introduites dans C# 8.0.  Vous pouvez toujours utiliser la bibliothèque avec les versions précédentes du langage C#, mais la nouvelle syntaxe ne sera pas disponible. Pour utiliser la syntaxe complète, nous vous recommandons d’effectuer la compilation avec la version 3.0 ou une version ultérieure du [kit SDK .NET Core](https://dotnet.microsoft.com/download) et la [version du langage](/dotnet/csharp/language-reference/configure-language-version#override-a-default) `latest`. Si vous utilisez Visual Studio, les versions antérieures à Visual Studio 2019 ne sont pas compatibles avec les outils nécessaires à la génération de projets C# 8.0. Visual Studio 2019, y compris l’édition Community gratuite, est téléchargeable [ici](https://visualstudio.microsoft.com/vs/).
- Suivez les étapes de [Démarrage rapide](service-bus-quickstart-topics-subscriptions-portal.md) pour créer une rubrique Service Bus et des abonnements à cette rubrique. 

    > [!IMPORTANT]
    > Notez la chaîne de connexion à l’espace de noms, le nom de la rubrique et le nom de l’un des abonnements à la rubrique. Vous les utiliserez ultérieurement dans ce tutoriel.
 
## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique
Cette section montre comment créer une application console .NET Core pour envoyer des messages à une rubrique Service Bus. 

### <a name="create-a-console-application"></a>Création d’une application console

1. Démarrez Visual Studio 2019. 
1. Sélectionnez **Créer un projet**. 
1. Dans la boîte de dialogue **Créer un projet**, effectuez les étapes suivantes : Si vous ne voyez pas cette boîte de dialogue, sélectionnez **Fichier** dans le menu, sélectionnez **Nouveau**, puis **Projet**. 
    1. Sélectionnez **C#** en guise de langage de programmation.
    1. Sélectionnez **Console** comme type de l’application. 
    1. Sélectionnez **Application console** dans la liste des résultats. 
    1. Ensuite, sélectionnez **Suivant**. 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="Image montrant la boîte de dialogue Créer un projet avec C# et la console sélectionnés":::
1. Entrez **TopicSender** comme nom de projet, **ServiceBusTopicQuickStart** comme nom de solution, puis sélectionnez **Suivant**. 
1. Dans la page **Informations supplémentaires**, sélectionnez **Créer** pour créer la solution et le projet.

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.ServiceBus** :

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-topic"></a>Ajouter du code pour envoyer des messages à la rubrique 

1. Remplacez le contenu de **Program.cs** par le code suivant. Voici les étapes importantes du code.  
    1. Crée un objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) à l’aide de la chaîne de connexion à l’espace de noms. 
    1. Utilise la méthode `CreateSender` sur l’objet `ServiceBusClient` pour créer un objet [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) pour la rubrique Service Bus spécifique.     
    1. Crée un objet `ServiceBusMessageBatch` à l’aide de la méthode `ServiceBusSender.CreateMessageBatchAsync`.
    1. Ajoutez des messages au lot à l’aide de la méthode `ServiceBusMessageBatch.TryAddMessage`. 
    1. Envoie le lot de messages à la rubrique Service Bus avec la méthode `ServiceBusSender.SendMessagesAsync`.
    
        Pour plus d'informations, consultez les commentaires du code.
        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace TopicSender
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of your Service Bus topic
                static string topicName = "<TOPIC NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the sender used to publish messages to the topic
                static ServiceBusSender sender;
        
                // number of messages to be sent to the topic
                private const int numOfMessages = 3;
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
                    sender = client.CreateSender(topicName);
        
                    // create a batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
        
                    for (int i = 1; i <= 3; i++)
                    {
                        // try adding a message to the batch
                        if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                        {
                            // if it is too large for the batch
                            throw new Exception($"The message {i} is too large to fit in the batch.");
                        }
                    }
        
                    try
                    {
                        // Use the producer client to send the batch of messages to the Service Bus topic
                        await sender.SendMessagesAsync(messageBatch);
                        Console.WriteLine($"A batch of {numOfMessages} messages has been published to the topic.");
                    }
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await sender.DisposeAsync();
                        await client.DisposeAsync();
                    }
        
                    Console.WriteLine("Press any key to end the application");
                    Console.ReadKey();
                }
            }
        }    
        ```
1. Remplacez `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion de votre espace de noms Service Bus, et remplacez `<TOPIC NAME>` par le nom de votre rubrique Service Bus. 
1. Générez le projet et vérifiez qu’il ne présente pas d’erreurs. 
1. Exécutez le programme et attendez le message de confirmation.
    
    ```bash
    A batch of 3 messages has been published to the topic
    ```
1. Dans le portail Azure, procédez comme suit :
    1. Accédez à votre espace de noms Service Bus. 
    1. Dans la page **Vue d’ensemble**, dans le volet central inférieur, basculez vers l’onglet **Rubriques**, puis sélectionnez la rubrique Service Bus. Dans l’exemple suivant, il s’agit de `mytopic`.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Sélectionner la rubrique":::
    1. Dans la page **Rubrique Service Bus**, dans le graphique **Messages** de la section inférieure **Métriques**, vous pouvez voir trois messages entrants pour la rubrique. Si vous ne voyez pas cette valeur, attendez quelques minutes et actualisez la page pour voir une mise à jour du graphique. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Messages envoyés à la rubrique" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Sélectionnez l’abonnement dans le volet inférieur. Dans l’exemple suivant, il s’agit de **S1**. Dans la page **Abonnement Service Bus**, vous voyez que **Nombre de messages actifs** a pour valeur **3**. L’abonnement a reçu les trois messages que vous avez envoyés à la rubrique, mais aucun destinataire ne les a encore sélectionnés. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Messages reçus au niveau de l’abonnement" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    
## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement
Dans cette section, vous allez créer une application console .NET Core qui reçoit les messages de l’abonnement à la rubrique Service Bus. 

### <a name="create-a-project-for-the-receiver"></a>Créer un projet pour le récepteur

1. Dans la fenêtre Explorateur de solutions, cliquez avec le bouton droit sur la solution **ServiceBusTopicQuickStart**, pointez sur **Ajouter**, puis sélectionnez **Nouveau projet**. 
1. Sélectionnez **Application console**, puis **Suivant**. 
1. Entrez **SubscriptionReceiver** comme **Nom du projet**, puis sélectionnez **Suivant**. 
1. Sur la page **Informations supplémentaires**, sélectionnez **Créer**. 
1. Dans la fenêtre **Explorateur de solutions**, cliquez avec le bouton droit sur **SubscriptionReceiver**, puis sélectionnez **Définir comme projet de démarrage**. 

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Dans la fenêtre **Console du gestionnaire de package**, vérifiez que **SubscriptionReceiver** est sélectionné pour le **Projet par défaut**. Si ce n’est pas le cas, utilisez la liste déroulante pour sélectionner **SubscriptionReceiver**.
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.ServiceBus** :

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-receive-messages-from-the-subscription"></a>Ajouter du code pour recevoir des messages de l’abonnement
1. Remplacez le contenu de **Program.cs** par le code suivant. Voici les étapes importantes du code.
    Voici les étapes importantes du code :
    1. Crée un objet [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) à l’aide de la chaîne de connexion à l’espace de noms. 
    1. Appelle la méthode `CreateProcessor` sur l'objet `ServiceBusClient` pour créer un objet [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) pour la file d’attente Service Bus spécifiée. 
    1. Spécifie des gestionnaires pour les événements `ProcessMessageAsync` et `ProcessErrorAsync` de l’objet `ServiceBusProcessor`. 
    1. Démarre le traitement des messages en appelant `StartProcessingAsync` sur l' objet `ServiceBusProcessor`. 
    1. Lorsque l’utilisateur appuie sur une clé pour terminer le traitement, il appelle `StopProcessingAsync` sur l' objet `ServiceBusProcessor`. 

        Pour plus d'informations, consultez les commentaires du code.

        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace SubscriptionReceiver
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of the Service Bus topic
                static string topicName = "<SERVICE BUS TOPIC NAME>";
            
                // name of the subscription to the topic
                static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the processor that reads and processes messages from the subscription
                static ServiceBusProcessor processor;
        
                // handle received messages
                static async Task MessageHandler(ProcessMessageEventArgs args)
                {
                    string body = args.Message.Body.ToString();
                    Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");
        
                    // complete the message. messages is deleted from the subscription. 
                    await args.CompleteMessageAsync(args.Message);
                }
        
                // handle any errors when receiving messages
                static Task ErrorHandler(ProcessErrorEventArgs args)
                {
                    Console.WriteLine(args.Exception.ToString());
                    return Task.CompletedTask;
                }
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());
        
                    try
                    {
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
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await processor.DisposeAsync();
                        await client.DisposeAsync();
                    }
                }
            }
        }
        ```
1. Remplacez les espaces réservés par des valeurs correctes :
    - `<NAMESPACE CONNECTION STRING>` par la chaîne de connexion de votre espace de noms Service Bus
    - `<TOPIC NAME>` par le nom de votre rubrique Service Bus
    - `<SERVICE BUS - TOPIC SUBSCRIPTION NAME>` par le nom de l’abonnement à la rubrique. 
1. Générez le projet et vérifiez qu’il ne présente pas d’erreurs.
1. Exécutez l’application réceptrice. Vous devriez voir les messages reçus. Appuyez sur n’importe quelle touche pour arrêter le récepteur et l’application. 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1 from subscription: S1
    Received: Message 2 from subscription: S1
    Received: Message 3 from subscription: S1
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. Vérifiez à nouveau le portail. 
    - Dans la page **Rubrique Service Bus**, dans le graphique **Messages**, vous voyez trois messages entrants et trois messages sortants. Si vous ne voyez pas ces valeurs, attendez quelques minutes et actualisez la page pour voir une mise à jour du graphique. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Messages envoyés et reçus" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
    - Dans la page **Abonnement Service Bus**, vous voyez que **Nombre de messages actifs** est égal à zéro. Cela est dû au fait qu’un destinataire a reçu des messages de cet abonnement et a terminé les messages. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Nombre de messages actifs au niveau de l’abonnement à la fin" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
        


## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation et les exemples suivants :

- [Bibliothèque de client Azure Service Bus pour .NET – Fichier Lisez-moi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Échantillons .NET pour Azure Service Bus sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Informations de référence sur l’API .NET](/dotnet/api/azure.messaging.servicebus?preserve-view=true)
