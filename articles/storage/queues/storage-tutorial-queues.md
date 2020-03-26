---
title: 'Tutoriel : Utiliser des files d’attente de stockage Azure - Stockage Azure'
description: Tutoriel expliquant comment utiliser le service File d’attente Azure pour créer des files d’attente, et pour insérer, obtenir et supprimer des messages.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75968195"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Tutoriel : Utiliser des files d’attente de stockage Azure

Stockage File d’attente Azure implémente des files d’attente cloud pour permettre la communication entre les composants d’une application distribuée. Chaque file d’attente gère une liste de messages qui peuvent être ajoutés par un composant expéditeur et traités par un composant récepteur. Avec une file d’attente, votre application peut être mise à l’échelle immédiatement pour répondre à la demande. Cet article explique les étapes de base pour travailler avec une file d’attente de stockage Azure.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Créer un compte de stockage Azure
> - Créer l’application
> - Ajouter la prise en charge du code asynchrone
> - Créer une file d’attente
> - Insérer des messages dans une file d’attente
> - Enlever des messages d’une file d’attente
> - Supprimer une file d’attente vide
> - Vérifier les arguments de la ligne de commande
> - Générer et exécuter l’application

## <a name="prerequisites"></a>Prérequis

- Procurez-vous votre copie gratuite de l’éditeur [Visual Studio Code](https://code.visualstudio.com/download) multiplateforme.
- Téléchargez et installez le [SDK .NET Core](https://dotnet.microsoft.com/download).
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

Créez d’abord un compte de stockage Azure. Pour obtenir un guide pas à pas de la création d’un compte de stockage, consultez le guide de démarrage rapide [Créer un compte de stockage](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json).

## <a name="create-the-app"></a>Créer l’application

Créez une application .NET Core nommée **QueueApp**. Par souci pratique, cette application envoie et reçoit des messages via la file d’attente.

1. Dans une fenêtre de console (par exemple CMD, PowerShell ou Azure CLI), utilisez la commande `dotnet new` pour créer une application de console avec le nom **QueueApp**. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Passez au dossier **QueueApp** nouvellement créé et générez l’application pour vérifier que tout va bien.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Vous devez normalement voir des résultats similaires à ceci :

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Ajouter la prise en charge du code asynchrone

Comme l’application utilise des ressources cloud, le code s’exécute de façon asynchrone. Cependant, les mots clés **async** et **await** de C# n’étaient pas des mots clés valides dans les méthodes **Main** jusqu’à C# 7.1. Vous pouvez facilement passer à ce compilateur via un indicateur dans le fichier **csproj**.

1. Sur la ligne de commande dans le répertoire du projet, tapez `code .` pour ouvrir Visual Studio Code dans le répertoire actif. Laissez ouverte la fenêtre de ligne de commande. Il y aura d’autres commandes à exécuter plus tard. Si vous êtes invité à ajouter des ressources C# nécessaires pour générer et déboguer, cliquez sur le bouton **Oui**.

2. Ouvrez le fichier **QueueApp.csproj** dans l’éditeur.

3. Ajoutez `<LangVersion>7.1</LangVersion>` dans le premier **PropertyGroup** dans le fichier de build. Veillez à ajouter seulement la balise **LangVersion**, car votre **TargetFramework** peut être différent selon la version de .NET que vous avez installée.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Enregistrez le fichier **QueueApp.csproj**.

5. Ouvrez le fichier source **Program.cs** et mettez à jour la méthode **Main** pour qu’elle s’exécute de façon asynchrone. Remplacez **void** par une valeur de retour **async Task**.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Enregistrez le fichier **Program.cs**.

## <a name="create-a-queue"></a>Créer une file d’attente

1. Installez les packages **Microsoft.Azure.Storage.Common** et **Microsoft.Azure.Storage.Queue** dans le projet à l'aide de la commande `dotnet add package`. Exécutez les commandes dotnet suivantes à partir du dossier du projet dans la fenêtre de console.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. En haut du fichier **Program.cs**, ajoutez les espaces de noms suivants juste après l’instruction `using System;`. Cette application utilise des types provenant de ces espaces de noms pour se connecter à Stockage Azure et pour utiliser des files d’attente.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Enregistrez le fichier **Program.cs**.

### <a name="get-your-connection-string"></a>Obtenir votre chaîne de connexion

La bibliothèque cliente utilise une chaîne de connexion pour établir votre connexion. Votre chaîne de connexion est disponible dans la section **Paramètres** de votre compte de stockage dans le portail Azure.

1. Dans votre navigateur web, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à votre compte de stockage dans le portail Azure.

3. Sélectionnez **Clés d’accès**.

4. Cliquez sur le bouton **Copier** à droite du champ **Chaîne de connexion**.

![Chaîne de connexion](media/storage-tutorial-queues/get-connection-string.png)

Celle-ci se présente au format suivant :

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Ajouter la chaîne de connexion à l’application

Ajoutez la chaîne de connexion à l’application afin qu’elle puisse accéder au compte de stockage.

1. Revenez à Visual Studio Code.

2. Dans la classe **Program**, ajoutez un membre `private const string connectionString =` destiné à contenir la chaîne de connexion.

3. Après le signe égal, collez la valeur de la chaîne que vous avez copiée précédemment dans votre portail Azure. La valeur **connectionString** sera unique pour votre compte.

4. Supprimez le code de « Hello World » dans **Main**. Votre code doit ressembler à ce qui suit, mais avec la valeur de votre chaîne de connexion unique.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Mettez à jour **Main** pour créer un objet **CloudQueue**, qui est passé plus tard dans les méthodes d’envoi et de réception.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Enregistrez le fichier .

## <a name="insert-messages-into-the-queue"></a>Insérer des messages dans la file d’attente

Créez une méthode pour envoyer un message dans la file d’attente. Ajoutez la méthode suivante à votre classe **Program**. Cette méthode obtient une référence de la file d’attente, puis crée une file d’attente si elle n’existe pas déjà en appelant [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Ensuite, elle ajoute le message à la file d’attente en appelant [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Ajoutez la méthode **SendMessageAsync** suivante à votre classe **Program**.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Enregistrez le fichier .

Un message doit être dans un format utilisable dans une requête XML avec encodage UTF-8 et avoir une taille maximale de 64 Ko. Si un message contient des données binaires, nous recommandons de l’encoder au format Base64.

Par défaut, un message peut être conservé pendant une durée maximale de 7 jours. Vous pouvez spécifier n’importe quel nombre positif pour la durée de vie du message. Pour ajouter un message qui n’expire pas, utilisez `Timespan.FromSeconds(-1)` dans votre appel à **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Enlever des messages d’une file d’attente

Créer une nouvelle méthode appelée **ReceiveMessageAsync**. Cette méthode reçoit un message de la file d’attente en appelant [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Une fois que le message est effectivement reçu, il est important de le supprimer de la file d’attente pour qu’il ne soit pas traité plusieurs fois. Une fois que le message est reçu, supprimez-le de la file d’attente en appelant [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Ajoutez la méthode **ReceiveMessageAsync** suivante à votre classe **Program**.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Enregistrez le fichier .

## <a name="delete-an-empty-queue"></a>Supprimer une file d’attente vide

C’est une bonne pratique à la fin d’un projet que de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Si la file d’attente existe mais qu’est vide, demandez à l’utilisateur s’il veut la supprimer.

1. Développez la méthode **ReceiveMessageAsync** pour y inclure une invite de suppression de la file d’attente vide.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Enregistrez le fichier .

## <a name="check-for-command-line-arguments"></a>Vérifier les arguments de la ligne de commande

Si des arguments de ligne de commande sont passés dans l’application, supposons qu’il s’agisse d’un message à ajouter à la file d’attente. Joignez ensemble les arguments pour en faire une chaîne. Ajoutez cette chaîne à la file d’attente des messages en appelant la méthode **SendMessageAsync** que nous avons ajoutée précédemment.

S’il n’y a pas d’arguments de ligne de commande, effectuez une opération de récupération. Appelez la méthode **ReceiveMessageAsync** pour récupérer le premier message de la file d’attente.

Enfin, attendez une entrée utilisateur avant de quitter en appelant **Console.ReadLine**.

1. Développez la méthode **Main** pour vérifier les arguments de la ligne de commande et attendre une entrée d’utilisateur.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Enregistrez le fichier .

## <a name="complete-code"></a>Code complet

Voici le code complet pour ce projet.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. À partir de la ligne de commande dans le répertoire du projet, exécutez la commande dotnet suivante pour générer le projet.

   ```console
   dotnet build
   ```

2. Une fois le projet correctement généré, exécutez la commande suivante pour ajouter le premier message à la file d’attente.

   ```console
   dotnet run First queue message
   ```

Cette sortie doit s’afficher :

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Exécutez l’application sans argument de ligne de commande pour recevoir et supprimer le premier message de la file d’attente.

   ```console
   dotnet run
   ```

4. Continuer à exécuter l’application jusqu’à ce que tous les messages soient supprimés. Si vous l’exécutez une fois de plus, vous obtenez un message indiquant que la file d’attente est vide et une invite pour supprimer la file d’attente.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

1. Créer une file d’attente
2. Ajouter et supprimer des messages dans une file d’attente
3. Supprimer une file d’attente de stockage Azure

Pour plus d’informations, consultez le guide de démarrage rapide des files d’attente Azure.

> [!div class="nextstepaction"]
> [Démarrage rapide des files d’attente](storage-quickstart-queues-portal.md)
