---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 42c961b81a254adef5e42c3c8916c9c081f548c8
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809719"
---
1. Dans la vue Solution (ou dans l’**Explorateur de solutions** dans Visual Studio), cliquez avec le bouton droit sur le dossier **Components**, cliquez sur **Get More Components...**, recherchez le composant **Google Cloud Messaging Client**, puis ajoutez-le au projet.
2. Ouvrez le fichier projet ToDoActivity.css et ajoutez l'instruction using suivante à la classe :

    ```csharp
    using Gcm.Client;
    ```

3. Dans la classe **ToDoActivity** , ajoutez le nouveau code suivant : 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Vous pouvez ainsi accéder à l’instance du client mobile depuis le processus de service de gestionnaire push.
4. Ajoutez le code ci-après à la méthode **OnCreate** après la création de **MobileServiceClient** :

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Votre **ToDoActivity** est maintenant prêt pour l'ajout de notifications push.
