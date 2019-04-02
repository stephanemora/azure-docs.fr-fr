---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce8496596f25b85719b8a6dff849ebf0fc3e5dc3
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405599"
---
Dans cette section, vous envoyez les dernières nouvelles sous forme de notifications modèles avec balises à partir de l’application console .NET. 

1. Dans Visual Studio, créez une application console Visual C# : a. Dans le menu, sélectionnez **Fichier** > **Nouveau** > **Projet**.
    b. Développez **Visual C#**, puis sélectionnez **Bureau Windows**. 
    c. Dans la liste des modèles, sélectionnez **Application console (.NET Framework)**. 
    d. Entrez un **nom** pour l’application. 
    e. Sélectionnez un **dossier** pour l’application.
    f. Cliquez sur **OK** pour créer le projet. 
2. Dans le menu principal de Visual Studio, sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**, puis dans la fenêtre de la console, tapez la chaîne suivante :
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Sélectionnez **Enter** (Entrer).  
    Cette action ajoute une référence au Kit de développement logiciel (SDK) Azure Notification Hubs à l’aide du [package NuGet Microsoft.Azure.Notification Hubs].

4. Ouvrez le fichier Program.cs et ajoutez l’instruction `using` suivante :
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. Dans la classe `Program` , ajoutez la méthode suivante ou remplacez-la si elle existe déjà :
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Ce code envoie une notification de modèle pour chacune des six balises du tableau de chaînes. L’utilisation des balises permet d’envoyer les notifications uniquement aux appareils des catégories inscrites.

5. Dans le code précédent, remplacez les espaces réservés `<hub name>` et `<connection string with full access>` par le nom de votre hub de notification et par la chaîne de connexion pour *DefaultFullSharedAccessSignature* du tableau de bord de votre hub de notifications.

6. Ajoutez les lignes suivantes à la méthode **Main** :
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Générez l’application console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[package NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
