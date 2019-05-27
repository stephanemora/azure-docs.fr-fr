---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140246"
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
