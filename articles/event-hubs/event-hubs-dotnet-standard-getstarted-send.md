---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant .NET (dernière version)
description: Cet article décrit la procédure à suivre pour créer une application .NET Core qui reçoit des événements d’Azure Event Hubs et lui en envoie à l’aide du dernier package Azure.Messaging.EventHubs.
ms.topic: quickstart
ms.date: 06/10/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: c6f28e46aff12b5730a1cc73f56fe9bd31805923
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112004369"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Réception d’événements en provenance et à destination d’Azure Event Hubs – .NET (Azure.Messaging.EventHubs) 
Ce guide de démarrage rapide montre comment recevoir des événements d’un hub d’événements et lui en envoyer à l’aide de la bibliothèque .NET **Azure.Messaging.EventHubs**. 

## <a name="prerequisites"></a>Prérequis
Si vous débutez avec Azure Event Hubs, consultez la [vue d’ensemble d’Event Hubs](event-hubs-about.md) avant de suivre ce guide de démarrage rapide. 

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- **Abonnement Microsoft Azure**. Pour utiliser les services Azure, y compris Azure Event Hubs, vous avez besoin d’un abonnement.  Si vous n’avez pas de compte Azure, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/) ou utiliser les avantages de votre abonnement MSDN quand vous [créez un compte](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. La bibliothèque cliente Azure Event Hubs utilise les nouvelles fonctionnalités introduites dans C# 8.0.  Vous pouvez toujours utiliser la bibliothèque avec les versions précédentes du langage C#, mais la nouvelle syntaxe ne sera pas disponible. Pour utiliser la syntaxe complète, il est recommandé d’effectuer la compilation avec la version 3.0 ou une version ultérieure du [kit SDK .NET Core](https://dotnet.microsoft.com/download) et la [version du langage](/dotnet/csharp/language-reference/configure-language-version#override-a-default) `latest`. Si vous utilisez Visual Studio, les versions antérieures à Visual Studio 2019 ne sont pas compatibles avec les outils nécessaires à la génération de projets C# 8.0. Visual Studio 2019, y compris l’édition Community gratuite, est téléchargeable [ici](https://visualstudio.microsoft.com/vs/).
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md). Ensuite, obtenez la **chaîne de connexion de l’espace de noms Event Hubs** en suivant les instructions à partir de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce guide de démarrage rapide.

## <a name="send-events"></a>Envoyer des événements 
Cette section montre comment créer une application console .NET Core pour envoyer des événements à un hub d’événements. 

### <a name="create-a-console-application"></a>Création d’une application console

1. Démarrez Visual Studio 2019. 
1. Sélectionnez **Créer un projet**. 
1. Dans la boîte de dialogue **Créer un projet**, effectuez les étapes suivantes : Si vous ne voyez pas cette boîte de dialogue, sélectionnez **Fichier** dans le menu, sélectionnez **Nouveau**, puis **Projet**. 
    1. Sélectionnez **C#** en guise de langage de programmation.
    1. Sélectionnez **Console** comme type de l’application. 
    1. Sélectionnez **Application console** dans la liste des résultats. 
    1. Ensuite, sélectionnez **Suivant**. 

        :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/new-send-project.png" alt-text="Image représentant la boîte de dialogue Nouveau projet":::
1. Entrez **EventHubsSender** comme nom de projet, **EventHubsQuickStart** comme nom de solution, puis sélectionnez **OK** pour créer le projet. 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/project-solution-names.png" alt-text="Image représentant la page permettant d’entrer le nom de la solution et celui du projet":::

### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-events-to-the-event-hub"></a>Écriture de code pour envoyer des événements au hub d’événements

1. Ajoutez les instructions `using` ci-après en haut du fichier **Program.cs** :

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion Event Hubs et le nom du hub d’événements.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";

        // number of events to be sent to the event hub
        private const int numOfEvents = 3;
    ```

    > [!NOTE]
    > Remplacez les valeurs d’espace réservé par la chaîne de connexion à votre espace de noms et le nom du hub d’événements. Concernant la chaîne de connexion, il doit s’agir de la chaîne de connexion au niveau de l’espace de noms.
3. Ajoutez la propriété statique suivante à la classe `Program`. Consultez les commentaires du code. 

    ```csharp
        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.
        static EventHubProducerClient producerClient;    
    ```
1. Remplacez la méthode `Main` par la méthode `async Main` suivante. Pour plus d’informations, consultez les commentaires du code. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            producerClient = new EventHubProducerClient(connectionString, eventHubName);

            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            for (int i = 1; i <= 3; i++)
            {
                if (! eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes($"Event {i}"))))
                {
                    // if it is too large for the batch
                    throw new Exception($"Event {i} is too large for the batch and cannot be sent.");
                }
            }

            try
            {
                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
            finally
            {
                await producerClient.DisposeAsync();
            }
        }
    ```
5. Générez le projet et vérifiez qu’il ne présente pas d’erreurs.
6. Exécutez le programme et attendez le message de confirmation. 

    ```csharp
    A batch of 3 events has been published.
    ```
1. Sur le Portail Azure, vous pouvez vérifier que le hub d’événements a reçu les événements. Basculez vers l’affichage **Messages** dans la section **Métriques**. Actualisez la page pour mettre à jour le graphique. Cela peut prendre quelques secondes pour indiquer que les messages ont été reçus. 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png" alt-text="Image de la page du Portail Azure permettant de vérifier que le hub d’événements a reçu les événements" lightbox="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png":::

    > [!NOTE]
    > Pour obtenir le code source complet avec des remarques plus détaillées, consultez [ce fichier sur GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

## <a name="receive-events"></a>Recevoir des événements
Cette section explique comment écrire une application console .NET Core qui reçoit des événements d’un hub d’événements à l’aide d’un processeur d’événements. Le processeur d’événements simplifie la réception d’événements provenant des hubs d’événements en gérant les points de contrôle persistants et les réceptions parallèles de ces hubs d’événements. Un processeur d’événements est associé à un hub d’événements et à un groupe de consommateurs spécifiques. Il reçoit les événements de plusieurs partitions dans le hub d’événements, puis les passe à un délégué de gestionnaire à des fins de traitement en utilisant le code que vous fournissez. 


> [!WARNING]
> Si vous exécutez ce code sur **Azure Stack Hub**, vous rencontrez des erreurs à l’exécution, sauf si vous ciblez une version spécifique de l’API Stockage. Ceci est dû au fait que le SDK Event Hubs utilise la dernière API Stockage Azure disponible dans Azure, qui peut ne pas être pas disponible sur votre plateforme Azure Stack Hub. Azure Stack Hub peut prendre en charge une autre version du SDK Stockage Blob que celles généralement disponibles sur Azure. Si vous utilisez Stockage Blob Azure comme magasin de points de contrôle, vérifiez la [version de l’API Stockage Azure prise en charge pour votre build Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) et ciblez cette version dans votre code. 
>
> Par exemple, si vous exécutez sur Azure Stack Hub version 2005, la version la plus élevée disponible pour le service Stockage est la version 2019-02-02. Par défaut, la bibliothèque de client du SDK Event Hubs utilise la version la plus récente disponible sur Azure (2019-07-07 au moment de la publication du SDK). Dans ce cas, en plus des étapes suivantes de cette section, vous devrez également ajouter du code pour cibler la version 2019-02-02 de l’API du service Stockage. Pour obtenir un exemple sur la façon de cibler une version spécifique de l’API de stockage, consultez [cet exemple sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Créer un compte Stockage Azure et un conteneur de blobs
Dans ce guide de démarrage rapide, vous utilisez Stockage Azure comme magasin de points de contrôle. Suivez les étapes ci-dessous pour créer un compte Stockage Azure. 

1. [Création d’un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. Créer un [conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenir la chaîne de connexion au compte de stockage](../storage/common/storage-configure-connection-string.md)

    Notez la chaîne de connexion et le nom du conteneur. Vous les utiliserez dans le code de réception. 


### <a name="create-a-project-for-the-receiver"></a>Créer un projet pour le récepteur

1. Dans la fenêtre Explorateur de solutions, cliquez avec le bouton droit sur la solution **EventHubQuickStart**, pointez sur **Ajouter**, puis sélectionnez **Nouveau projet**. 
1. Sélectionnez **Application console**, puis **Suivant**. 
1. Entrez **EventHubsReceiver** pour **Nom du projet**, puis sélectionnez **Créer**. 
1. Dans la fenêtre **Explorateur de solutions**, cliquez avec le bouton droit sur **EventHubsReceiver**, puis sélectionnez **Définir comme projet de démarrage**. 

### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Dans la fenêtre **Console du gestionnaire de package**, vérifiez que **EventHubsReceiver** est sélectionné pour le **Projet par défaut**. Si ce n’est pas le cas, utilisez la liste déroulante pour sélectionner **EventHubsReceiver**.
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.EventHubs.Processor** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Mettre à jour la méthode Main 

1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs**.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion Event Hubs et le nom du hub d’événements. Remplacez les espaces réservés entre crochets par les valeurs appropriées que vous avez obtenues lors de la création du hub d’événements. Remplacez les espaces réservés entre crochets par les valeurs appropriées que vous avez obtenues lors de la création du hub d’événements et du compte de stockage (clés d’accès, chaîne de connexion principale). Vérifiez que `{Event Hubs namespace connection string}` est la chaîne de connexion au niveau de l’espace de noms, et pas la chaîne de concentrateur d’événements.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Ajoutez les propriétés statiques suivantes à la classe `Program`. 

    ```csharp
        static BlobContainerClient storageClient;

        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.        
        static EventProcessorClient processor;    
    ```
1. Remplacez la méthode `Main` par la méthode `async Main` suivante. Pour plus d’informations, consultez les commentaires du code. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }
    ```
1. À présent, ajoutez à la classe les méthodes de gestionnaire d’erreurs et d’événements suivantes. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Générez le projet et vérifiez qu’il ne présente pas d’erreurs.

    > [!NOTE]
    > Pour obtenir le code source complet avec des remarques plus détaillées, consultez [ce fichier sur GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Exécutez l’application réceptrice. 
1. Vous devriez voir un message indiquant que les événements ont été reçus. 

    ```bash
    Received event: Event 1
    Received event: Event 2
    Received event: Event 3    
    ```
    Ces événements sont les trois événements que vous avez envoyés au hub d’événements en exécutant le programme émetteur. 


## <a name="next-steps"></a>Étapes suivantes
Consultez les exemples sur GitHub. 

- [Exemples Event Hubs sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Exemples de processeurs d’événement sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Exemple de contrôle d’accès en fonction du rôle Azure (Azure RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
