---
title: Publier et traiter des messages dans les rubriques Atlas Kafka d’Azure Purview via Event Hubs à l’aide de .NET
description: Cet article décrit la procédure à suivre pour créer une application .NET Core qui reçoit des événements provenant des rubriques Atlas Kafka d’Azure Purview et leur en envoie, à l’aide du package Azure.Messaging.EventHubs le plus récent.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589726"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Publier et traiter des messages dans les rubriques Atlas Kafka d’Azure Purview via Event Hubs à l’aide de .NET 
Ce guide de démarrage rapide montre comment recevoir des événements provenant des rubriques Atlas Kafka d’Azure Purview et leur en envoyer, à l’aide de la bibliothèque .NET **Azure.Messaging.EventHubs**. 

> [!IMPORTANT]
> Un hub d’événements managé est créé lors de la création d’un compte Purview. Pour plus d’informations, consultez [Création d’un compte Purview](create-catalog-portal.md). Vous pouvez publier des messages dans la rubrique Kafka ATLAS_HOOK du hub d’événements pour que Purview les consomme et les traite. Purview informe l’utilisateur des modifications qui ont été apportées à la rubrique Kafka ATLAS_ENTITIES du hub d’événements pour qu’il puisse les consommer et les traiter. Ce guide de démarrage rapide utilise la nouvelle bibliothèque **Azure.Messaging.EventHubs**. 


## <a name="prerequisites"></a>Prérequis
Si vous débutez avec Azure Event Hubs, consultez la [vue d’ensemble d’Event Hubs](../event-hubs/event-hubs-about.md) avant de suivre ce guide de démarrage rapide. 

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- **Abonnement Microsoft Azure**. Pour utiliser les services Azure, y compris Azure Event Hubs, vous avez besoin d’un abonnement.  Si vous n’avez pas de compte Azure, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/) ou utiliser les avantages de votre abonnement MSDN quand vous [créez un compte](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. La bibliothèque cliente Azure Event Hubs utilise les nouvelles fonctionnalités introduites dans C# 8.0.  Vous pouvez toujours utiliser la bibliothèque avec les versions précédentes du langage C#, mais la nouvelle syntaxe ne sera pas disponible. Pour utiliser la syntaxe complète, il est recommandé d’effectuer la compilation avec la version 3.0 ou une version ultérieure du [kit SDK .NET Core](https://dotnet.microsoft.com/download) et la [version du langage](/dotnet/csharp/language-reference/configure-language-version#override-a-default) `latest`. Si vous utilisez Visual Studio, les versions antérieures à Visual Studio 2019 ne sont pas compatibles avec les outils nécessaires à la génération de projets C# 8.0. Visual Studio 2019, y compris l’édition Community gratuite, est téléchargeable [ici](https://visualstudio.microsoft.com/vs/).

## <a name="publish-messages-to-purview"></a>Publier des messages dans Purview 
Cette section montre comment créer une application console .NET Core pour envoyer des événements à Purview via la rubrique Kafka **ATLAS_HOOK** d’un hub d’événements. 

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

Créez ensuite une application console .NET en C# dans Visual Studio :

1. Lancez **Visual Studio**.
2. Dans la fenêtre Démarrer, sélectionnez **Créer un projet** > **Application console (.NET Framework)** . .NET version 4.5.2 ou ultérieure est nécessaire.
3. Dans **Nom du projet**, entrez **PurviewKafkaProducer**.
4. Sélectionnez **Créer** pour créer le projet.

### <a name="create-a-console-application"></a>Création d’une application console

1. Démarrez Visual Studio 2019. 
1. Sélectionnez **Créer un projet**. 
1. Dans la boîte de dialogue **Créer un projet**, effectuez les étapes suivantes : Si vous ne voyez pas cette boîte de dialogue, sélectionnez **Fichier** dans le menu, sélectionnez **Nouveau**, puis **Projet**. 
    1. Sélectionnez **C#** en guise de langage de programmation.
    1. Sélectionnez **Console** comme type de l’application. 
    1. Sélectionnez **Application console (.NET Core)** dans la liste des résultats. 
    1. Ensuite, sélectionnez **Suivant**. 


### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
1. Exécutez la commande suivante pour installer le package NuGet **Azure.Messaging.EventHubs** et le package NuGet **Azure.Messaging.EventHubs.Producer** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un hub d’événements

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
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Vous pouvez obtenir l’espace de noms du hub d’événements qui est associé au compte Purview en accédant aux chaînes de connexion principale et secondaire du point de terminaison Atlas Kafka sous l’onglet Propriétés du compte Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Espace de noms du hub d’événements":::

    Pour envoyer des messages vers Purview, le nom du hub d’événements doit être **ATLAS_HOOK**.

3. Remplacez la méthode `Main` par la méthode `async Main` suivante, et ajoutez un `async ProduceMessage` pour envoyer (push) des messages vers Purview. Pour plus d’informations, consultez les commentaires du code. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Générez le projet et vérifiez qu’il ne présente pas d’erreurs.
6. Exécutez le programme et attendez le message de confirmation. 

    > [!NOTE]
    > Pour obtenir le code source complet avec des remarques plus détaillées, consultez [ce fichier sur GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Exemple de création d’un message JSON d’entité pour créer une table SQL comprenant deux colonnes.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Consommer des messages dans Purview
Cette section explique comment écrire une application console .NET Core qui reçoit des messages d’un hub d’événements à l’aide d’un processeur d’événements. Vous devez utiliser le hub d’événements ATLAS_ENTITIES pour recevoir des messages de Purview. Le processeur d’événements simplifie la réception d’événements provenant des hubs d’événements en gérant les points de contrôle persistants et les réceptions parallèles à partir de ces hubs d’événements. 

> [!WARNING]
> Si vous exécutez ce code sur Azure Stack Hub, vous rencontrerez des erreurs d’exécution, sauf si vous ciblez une version spécifique de l’API Stockage. Ceci est dû au fait que le SDK Event Hubs utilise la dernière API Stockage Azure disponible dans Azure, qui peut ne pas être pas disponible sur votre plateforme Azure Stack Hub. Azure Stack Hub peut prendre en charge une autre version du SDK Stockage Blob que celles généralement disponibles sur Azure. Si vous utilisez Stockage Blob Azure comme magasin de points de contrôle, vérifiez la [version de l’API Stockage Azure prise en charge pour votre build Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) et ciblez cette version dans votre code. 
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
1. Sélectionnez **Application console (.NET Core)** , puis sélectionnez **Suivant**. 
1. Entrez **PurviewKafkaConsumer** pour **Nom du projet**, puis sélectionnez **Créer**. 

### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

1. Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** à partir du menu. 
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
    
    Vous pouvez obtenir l’espace de noms du hub d’événements qui est associé au compte Purview en accédant aux chaînes de connexion principale et secondaire du point de terminaison Atlas Kafka sous l’onglet Propriétés du compte Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Espace de noms du hub d’événements":::

    Pour envoyer des messages vers Purview, le nom du hub d’événements doit être **ATLAS_ENTITIES**.

3. Remplacez la méthode `Main` par la méthode `async Main` suivante. Pour plus d’informations, consultez les commentaires du code. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

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

### <a name="sample-message-received-from-purview"></a>Exemple de message reçu de Purview

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas prend en charge les types d’opérations suivants : **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. L’envoi (push) de messages vers Purview est activé par défaut. Si le scénario implique la lecture de données dans Purview, contactez-nous car nous devrons les ajouter à la liste verte (indiquez l’ID d’abonnement et le nom du compte Purview).


## <a name="next-steps"></a>Étapes suivantes
Consultez les exemples sur GitHub. 

- [Exemples Event Hubs sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Exemples de processeurs d’événement sur GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Présentation des notifications Atlas](https://atlas.apache.org/2.0.0/Notifications.html)