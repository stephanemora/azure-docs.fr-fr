---
title: Envoyer des événements vers Azure Event Hubs avec .NET Standard | Microsoft Docs
description: Prise en main de l’envoi d’événements vers Event Hubs dans .NET Standard
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
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: e826dcdbc6d32e6f0ad6ddf72a95869c96af6d69
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456522"
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Bien démarrer avec l’envoi de messages vers Azure Event Hubs dans .NET Standard
Event Hubs constitue un service qui traite de grandes quantités de données d'événement (télémétrie) à partir de périphériques et d'applications connectés. Après avoir collecté des données dans les concentrateurs d’événements, vous pouvez les stocker à l’aide d’un cluster de stockage ou les transformer à l’aide d’un fournisseur d’analyses en temps réel. Cette fonctionnalité de collecte et de traitement d’événements à grande échelle représente un élément clé des architectures d’applications modernes, notamment l’Internet des objets (IoT). Pour une présentation détaillée des Event Hubs, consultez [Vue d’ensemble des Event Hubs](event-hubs-about.md) et [Fonctionnalités des Event Hubs](event-hubs-features.md).

Ce didacticiel montre également comment envoyer des événements vers un concentrateur d’événements à l’aide d’une application de console écrite en C# avec .NET Core. 

> [!NOTE]
> Vous pouvez télécharger ce démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), remplacer les chaînes `EventHubConnectionString` et `EventHubName` par vos valeurs de concentrateur d’événements, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

## <a name="prerequisites"></a>Prérequis
* [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017, mais Visual Studio 2015 est également pris en charge.
* [Outils Visual Studio 2015 ou 2017 .NET Core](http://www.microsoft.com/net/core). 

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Création d’un espace de noms Event Hubs et d’un concentrateur d’événements
La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes de ce didacticiel.

## <a name="create-a-console-application"></a>Création d’une application console

Démarrez Visual Studio. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**. Créez une application console .NET Core.

![Nouveau projet][1]

## <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

Ajoutez le package NuGet de bibliothèque standard .NET [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) à votre projet en procédant comme suit : 

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, puis recherchez « Microsoft.Azure.EventHubs » et sélectionnez le package **Microsoft.Azure.EventHubs**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un hub d’événements

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs :

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Ajoutez des constantes à la classe `Program` pour le chemin de l’entité et la chaîne de connexion Event Hubs (nom du concentrateur d’événements individuel). Remplacez les espaces réservés entre crochets par les valeurs appropriées obtenues lors de la création du concentrateur d’événements. Vérifiez que `{Event Hubs connection string}` est la chaîne de connexion au niveau de l’espace de noms, et pas la chaîne de concentrateur d’événements. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Ajoutez une nouvelle méthode nommée `SendMessagesToEventHub` à la classe `Program`, comme suit :

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Ajoutez le code suivant à la méthode `Main` dans la classe `Program` :

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Voici à quoi doit ressembler votre fichier Program.cs.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.

Félicitations ! Vous venez d’envoyer des messages à un concentrateur d’événements.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez envoyé des messages à un concentrateur d’événements à l’aide de .NET Standard. Pour découvrir comment recevoir des événements depuis un concentrateur d’événements à l’aide de .NET Standard, consultez [Recevoir des événements depuis Event Hub - .NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md).

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png
