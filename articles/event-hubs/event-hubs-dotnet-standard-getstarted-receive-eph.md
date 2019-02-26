---
title: Recevoir des événements à l’aide d’une application .NET Core - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application .NET Core qui reçoit des événements avec EventProcessorHost.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: a3c4bc741cef60576bec17cd3257914132b72666
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56452623"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-core"></a>Découvrir comment recevoir des messages à l’aide de l’hôte du processeur d’événements dans .NET Core
Event Hubs constitue un service qui traite de grandes quantités de données d'événement (télémétrie) à partir de périphériques et d'applications connectés. Après avoir collecté des données dans les concentrateurs d’événements, vous pouvez les stocker à l’aide d’un cluster de stockage ou les transformer à l’aide d’un fournisseur d’analyses en temps réel. Cette fonctionnalité de collecte et de traitement d’événements à grande échelle représente un élément clé des architectures d’applications modernes, notamment l’Internet des objets (IoT). Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce didacticiel explique comment écrire une application console .NET Core qui reçoit des messages d’un hub d’événements à l’aide de l’[Hôte du processeur d’événements](event-hubs-event-processor-host.md). [L’hôte du processeur d’événements](event-hubs-event-processor-host.md) est une classe .NET qui simplifie la réception d’événements provenant de concentrateurs d’événements grâce à la gestion des points de contrôle permanents et des réceptions en parallèle de ces concentrateurs d’événements. L’hôte du processeur d’événements vous permet de répartir des événements sur plusieurs récepteurs, même quand ils sont hébergés dans des nœuds différents. Cet exemple illustre l’utilisation de l’hôte du processeur d’événements pour un récepteur unique. L’exemple [Traitement d’événement mis à l’échelle](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) illustre l’utilisation de l’hôte du processeur d’événements avec plusieurs récepteurs.

> [!NOTE]
> Vous pouvez télécharger ce démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), remplacer `EventHubConnectionString` et `EventHubName`, `StorageAccountName`, `StorageAccountKey`, ainsi que les chaînes `StorageContainerName` par vos valeurs d’Event Hub, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

## <a name="prerequisites"></a>Prérequis
* [Microsoft Visual Studio 2015 ou 2017](https://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017, mais Visual Studio 2015 est également pris en charge.
* [Outils Visual Studio 2015 ou 2017 .NET Core](https://www.microsoft.com/net/core).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Création d’un espace de noms Event Hubs et d’un concentrateur d’événements
La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes de ce tutoriel.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Création d’une application console

Démarrez Visual Studio. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**. Créez une application console .NET Core.

![Nouveau projet][2]

## <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

Ajoutez les packages NuGet de la bibliothèque .NET Standard [ **Microsoft.Azure.EventHubs** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) et [ **Microsoft.Azure.EventHubs.Processor** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) à votre projet en effectuant les étapes suivantes : 

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, recherchez **Microsoft.Azure.EventHubs**, puis sélectionnez le package **Microsoft.Azure.EventHubs**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.
3. Répétez les étapes 1 et 2 et installez le package **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Implémentation de l’interface IEventProcessor

1. Dans l’Explorateur de solutions, cliquez sur **Ajouter**, puis sur **Classe**. Nommez la nouvelle classe **SimpleEventProcessor**.

2. Ouvrez le fichier SimpleEventProcessor.cs et ajoutez les instructions `using` suivantes au début du fichier.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implémentez l’interface `IEventProcessor`. Remplacez tout le contenu de la classe `SimpleEventProcessor` par le code suivant :

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Mettre à jour la méthode Main pour utiliser SimpleEventProcessor

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion du concentrateur d’événements, le nom du concentrateur d’événements, le nom du conteneur de compte de stockage, le nom du compte de stockage et la clé du compte de stockage. Ajoutez le code suivant, en remplaçant les espaces réservés par les valeurs correspondantes.

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Ajoutez la ligne de code suivante à la méthode `Main` :

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Voici à quoi doit ressembler votre fichier Program.cs :

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.

Félicitations ! Vous recevez maintenant les messages d’un concentrateur d’événements à l’aide de l’hôte du processeur d’événements.

> [!NOTE]
> Ce didacticiel utilise une seule instance d' [EventProcessorHost](event-hubs-event-processor-host.md). Pour augmenter le débit, nous recommandons d’exécuter plusieurs instances d’[EventProcessorHost](event-hubs-event-processor-host.md), comme illustré dans l’exemple de [traitement d’événement mis à l’échelle](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). Dans ces cas, les différentes instances se coordonnent automatiquement entre elles afin d’équilibrer la charge des événements reçus. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez créé une application .NET Core qui a reçu des messages d’un Event Hub. Pour découvrir comment envoyer des événements à un Event Hub à l’aide de .NET Core, consultez [Envoyer des événements à un Event Hub - .NET Core](event-hubs-dotnet-standard-getstarted-send.md).


[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
