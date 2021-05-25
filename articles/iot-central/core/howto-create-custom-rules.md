---
title: Étendre Azure IoT Central avec des notifications et des règles personnalisées | Microsoft Docs
description: En tant que développeur de solutions, configurez une application IoT Central afin d’envoyer des notifications par e-mail lorsqu’un appareil cesse d’envoyer des données de télémétrie. Cette solution utilise Azure Stream Analytics, Azure Functions et SendGrid.
author: philmea
ms.author: philmea
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 2eb776add1e4a53edc6f4f57983074af31d8f52d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108750038"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Étendre Azure IoT Central avec des règles personnalisées à l’aide de Stream Analytics, d’Azure Functions et de SendGrid

Ce guide pratique vous montre comment étendre votre application IoT Central avec des notifications et des règles personnalisées. L’exemple montre l’envoi d’une notification à un opérateur lorsqu’un appareil cesse d’envoyer des données de télémétrie. La solution utilise une requête [Azure Stream Analytics](../../stream-analytics/index.yml) pour détecter le moment où un appareil a arrêté d’envoyer des données de télémétrie. Le travail Stream Analytics utilise [Azure Functions](../../azure-functions/index.yml) pour envoyer des notifications par e-mail via [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Ce guide pratique vous montre comment étendre IoT Central au-delà de ce qu’il peut déjà faire avec les actions et règles intégrées.

Dans ce guide pratique, vous allez apprendre à effectuer les opérations suivantes :

* Transmettre des données de télémétrie en continu à partir d’une application IoT Central à l’aide de l’*exportation de données continue*.
* Créer une requête Stream Analytics qui détecte le moment où un appareil a arrêté d’envoyer des données.
* Envoyer une notification par e-mail à l’aide des services Azure Functions et SendGrid.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

### <a name="iot-central-application"></a>Application IoT Central

Créez une application IoT Central sur le site web [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Plan tarifaire | standard |
| Modèle d’application | Analytique en magasin – Monitorage des conditions |
| Nom de l'application | Acceptez la valeur par défaut ou choisissez votre propre nom |
| URL | Acceptez la valeur par défaut ou choisissez votre propre préfixe d’URL unique |
| Répertoire | Votre locataire Azure Active Directory |
| Abonnement Azure | Votre abonnement Azure |
| Région | Région la plus proche |

Les exemples et captures d’écran de cet article concernent la région **États-Unis**. Choisissez un emplacement proche de vous et assurez-vous de créer toutes vos ressources dans la même région.

Ce modèle d’application comprend deux appareils à thermostat simulés qui envoient des données de télémétrie.

### <a name="resource-group"></a>Resource group

Utilisez le [portail Azure pour créer un groupe de ressources](https://portal.azure.com/#create/Microsoft.ResourceGroup) appelé **DetectStoppedDevices** afin de contenir les autres ressources que vous créez. Créez vos ressources Azure dans le même emplacement que votre application IoT Central.

### <a name="event-hubs-namespace"></a>Espace de noms Event Hubs

Utilisez le [portail Azure pour créer un espace de noms Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Name    | Choisissez le nom de votre espace de noms |
| Niveau tarifaire | De base |
| Subscription | Votre abonnement |
| Resource group | DetectStoppedDevices |
| Location | USA Est |
| Unités de débit | 1 |

### <a name="stream-analytics-job"></a>Travail Stream Analytics

Utilisez le [portail Azure pour créer un travail Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Name    | Choisissez le nom de votre travail |
| Subscription | Votre abonnement |
| Resource group | DetectStoppedDevices |
| Location | USA Est |
| Environnement d’hébergement | Cloud |
| Unités de diffusion en continu | 3 |

### <a name="function-app"></a>Conteneur de fonctions

Utilisez le [portail Azure pour créer une application de fonction](https://portal.azure.com/#create/Microsoft.FunctionApp) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom de l’application    | Choisissez le nom de votre application de fonction |
| Subscription | Votre abonnement |
| Resource group | DetectStoppedDevices |
| Système d''exploitation | Windows |
| Plan d’hébergement | Plan de consommation |
| Location | USA Est |
| Pile d’exécution | .NET |
| Stockage | Création |

### <a name="sendgrid-account-and-api-keys"></a>Compte SendGrid et clés API

Si vous n’avez pas de compte Sendgrid, [créez-en un gratuitement](https://app.sendgrid.com/) avant de commencer.

1. Dans les paramètres du tableau de bord Sendgrid, dans le menu de gauche, sélectionnez **Clés API**.
1. Cliquez sur **Créer une clé API**.
1. Nommez la nouvelle clé API **AzureFunctionAccess.** .
1. Cliquez sur **Créer et afficher**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Capture d’écran de la clé API Créer SendGrid":::.

Vous recevez ensuite une clé API. Enregistrez cette chaîne à des fins d’utilisation ultérieure.

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Vous pouvez configurer une application IoT Central pour exporter en continu des données de télémétrie vers un Event Hub. Dans cette section, vous allez créer un Event Hub pour recevoir des données de télémétrie depuis votre application IoT Central. L’Event Hub fournit les données de télémétrie à votre travail Stream Analytics afin qu’elles soient traitées.

1. Dans le portail Azure, accédez à votre espace de noms Event Hubs et sélectionnez **+ Event Hub**.
1. Nommez votre Event Hub **centralexport**, puis sélectionnez **Créer**.

Votre espace de noms Event Hubs se présente comme la capture d’écran suivante : 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Les messages du journal de la fonction s’affichent dans le panneau **Journaux** :

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Ajouter une requête Stream Analytics

La solution utilise une requête Stream Analytics pour détecter le moment où un appareil a arrêté d’envoyer des données de télémétrie pendant plus de 120 secondes. La requête utilise les données de télémétrie de l’Event Hub comme entrée. Le travail envoie les résultats de la requête à l’application de fonction. Dans cette section, vous configurez un travail Azure Stream Analytics :

1. Sur le Portail Azure, accédez à votre travail Stream Analytics ; sous **Topologie des travaux**, sélectionnez **Entrées**, puis **+ Ajouter une entrée de flux** et enfin **Event Hub**.
1. Utilisez les informations dans le tableau suivant pour configurer l’entrée à l’aide de l’Event Hub que vous avez créé précédemment, puis choisissez **Enregistrer** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Alias d’entrée | centraltelemetry |
    | Subscription | Votre abonnement |
    | Espace de noms Event Hub | Espace de noms de votre Event Hub |
    | Nom de l’Event Hub | Utiliser le nom existant : **centralexport** |

1. Sous **Topologie des travaux**, sélectionnez **Sorties** et choisissez **+ Ajouter**, puis **Fonction Azure**.
1. Utilisez les informations du tableau suivant pour configurer la sortie, puis cliquez sur **Enregistrer** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Alias de sortie | emailnotification |
    | Subscription | Votre abonnement |
    | Conteneur de fonctions | Votre application de fonction |
    | Fonction  | HttpTrigger1 |

1. Sous **Topologie des travaux**, sélectionnez **Requête**, puis remplacez la requête existante par le SQL suivant :

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Sélectionnez **Enregistrer**.
1. Pour lancer le travail Stream Analytics, choisissez **Vue d’ensemble**, **Démarrer**, puis **Maintenant** et enfin **Démarrer** :

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Capture d’écran de Stream Analytics":::.

## <a name="configure-export-in-iot-central"></a>Configurer l’exportation dans IoT Central 

Sur le site web [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), accédez à l’application IoT Central que vous avez créée.

Dans cette section, vous allez configurer l’application pour diffuser les données de télémétrie depuis ses appareils simulés vers votre Event Hub. Pour configurer l’exportation :

1. Accédez à la page **Exportation de données**, sélectionnez **+ Nouveau**, puis **Azure Event Hubs**.
1. Utilisez les paramètres suivants pour configurer l’exportation, puis sélectionnez **Enregistrer** : 

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom d’affichage | Exporter vers Event Hubs |
    | activé | Il en va |
    | Type de données à exporter | Télémétrie |
    | Enrichissements | Entrez la clé/valeur correspondant à la manière dont vous souhaitez organiser les données exportées | 
    | Destination | Créer et entrer des informations pour l’emplacement où les données seront exportées |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Capture d’écran de l’exportation de données":::.

Attendez que l’état de l’exportation soit **En cours d’exécution** avant de continuer.

## <a name="test"></a>Test

Pour tester la solution, vous pouvez désactiver l’exportation de données continue depuis IoT Central vers les appareils arrêtés simulés :

1. Dans votre application IoT Central, accédez à la page **Exportation de données** et sélectionnez la configuration d’exportation **Exporter vers Event Hubs**.
1. Définissez l’option **Activer** sur **Non**, puis cliquez sur **Enregistrer**.
1. Après au moins deux minutes, l’adresse de **destination** recevra au moins un e-mail similaire à l’exemple de contenu suivant :

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Nettoyer

Pour nettoyer à la suite de cette procédure et éviter les coûts inutiles, supprimez le groupe de ressources **DetectStoppedDevices** dans le portail Azure.

Vous pouvez supprimer l’application IoT Central depuis la page **Gestion** au sein de l’application.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez appris à effectuer les opérations suivantes :

* Transmettre des données de télémétrie en continu à partir d’une application IoT Central à l’aide de l’*exportation de données continue*.
* Créer une requête Stream Analytics qui détecte le moment où un appareil a arrêté d’envoyer des données.
* Envoyer une notification par e-mail à l’aide des services Azure Functions et SendGrid.

Maintenant que vous savez comment créer des notifications et des règles personnalisées, il est conseillé de suivre l’étape suivante qui consiste à apprendre comment [Étendre Azure IoT Central avec une analyse personnalisée](howto-create-custom-analytics.md).
