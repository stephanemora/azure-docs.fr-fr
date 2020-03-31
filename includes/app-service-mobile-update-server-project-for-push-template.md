---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68857379"
---
Dans cette section, vous mettez à jour le code dans votre projet de serveur principal Mobile Apps existant pour envoyer une notification Push chaque fois qu’un nouvel élément est ajouté. Ce processus est rendu possible par la fonctionnalité [modèle](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) d’Azure Notification Hubs, qui autorise l’envoi de notifications Push multiplateforme. Les différents clients sont inscrits pour les notifications Push à l’aide de modèles, et une notification Push universelle unique peut accéder à toutes les plates-formes clientes.

Choisissez l’une des procédures ci-après correspondant au type de votre projet de serveur principal &mdash;[projet de serveur principal .NET](#dotnet) ou [projet de serveur principal Node.js](#nodejs).

### <a name="net-back-end-project"></a><a name="dotnet"></a>Projet de serveur principal .NET

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur. Ensuite, sélectionnez **Gérer les packages NuGet**. Recherchez `Microsoft.Azure.NotificationHubs`, puis sélectionnez **Installer**. Ce processus installe la bibliothèque Notification Hubs pour l’envoi de notifications à partir du serveur principal.
2. Dans le projet de serveur, ouvrez **Contrôleurs** > **TodoItemController.cs**. Puis ajoutez les instructions using suivantes :

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. Dans la méthode **PostTodoItem**, ajoutez le code suivant après l’appel à **InsertAsync** :  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Ce processus envoie une notification de modèle contenant item.Text lorsqu’un nouvel élément est inséré.

4. Publier à nouveau le projet de serveur

### <a name="nodejs-back-end-project"></a><a name="nodejs"></a>Projet de serveur principal Node.js

1. Configurez votre projet back-end.
2. Remplacez le code existant dans todoitem.js par le code suivant :

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Ce processus envoie une notification de modèle contenant item.text lorsqu’un nouvel élément est inséré.

3. Quand vous modifiez le fichier sur votre ordinateur local, republiez le projet de serveur.
