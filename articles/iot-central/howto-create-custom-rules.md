---
title: Étendre Azure IoT Central avec des règles personnalisées et les notifications | Microsoft Docs
description: En tant que développeur de solutions, configurer une application IoT Central pour envoyer des notifications par courrier électronique quand un appareil cesse d’envoyer des données de télémétrie. Cette solution utilise Azure Stream Analytique et Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6140a8aea3fe0fe0a8f1c01cd1c97404c41f7a69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805980"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Étendre Azure IoT Central à l’aide des règles personnalisées qui envoient des notifications

Ce guide vous montre, en tant que développeur de solutions, comment étendre votre application IoT Central avec des règles personnalisées et les notifications. L’exemple montre l’envoi d’une notification à un opérateur lorsqu’un appareil cesse d’envoyer des données de télémétrie. La solution utilise un [Azure Stream Analytique](https://docs.microsoft.com/azure/stream-analytics/) requête pour détecter quand un appareil a arrêté l’envoi des données de télémétrie. L’Analytique Stream travail utilise [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) pour envoyer une notification envoie un e-mail à l’aide de [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Ce guide vous montre comment étendre IoT Central au-delà de ce qu’il peut faire déjà avec les règles intégrées et les actions.

Dans ce guide, vous apprendrez comment :

* Données de télémétrie à partir d’une application IoT Central à l’aide de Stream *exportation continue de données*.
* Créer une requête de Stream Analytique qui détecte quand un appareil a arrêté l’envoi des données.
* Envoyer une notification par courrier électronique à l’aide d’Azure Functions et services SendGrid.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

### <a name="iot-central-application"></a>Application IoT Central

Créer une application IoT Central à partir de la [Azure IoT Central - mes applications](https://aka.ms/iotcentral) page avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Plan de paiement | Paiement à l'utilisation |
| Modèle d’application | Exemple Contoso |
| Nom de l'application | Acceptez la valeur par défaut ou choisissez votre propre nom |
| URL | Acceptez la valeur par défaut ou choisissez votre propre préfixe d’URL unique |
| Répertoire | Votre client Azure Active Directory |
| Abonnement Azure | Votre abonnement Azure |
| Région | USA Est |

Les exemples et les captures d’écran dans cet article utilisent le **est des États-Unis** région. Choisissez un emplacement proche de vous et vérifiez que vous créez toutes vos ressources dans la même région.

### <a name="resource-group"></a>Groupe de ressources

Utilisez le [portail Azure pour créer un groupe de ressources](https://portal.azure.com/#create/Microsoft.ResourceGroup) appelé **DetectStoppedDevices** pour contenir les autres ressources que vous créez. Créez vos ressources Azure dans le même emplacement que votre application IoT Central.

### <a name="event-hubs-namespace"></a>Espace de noms Event Hubs

Utilisez le [portail Azure pour créer un espace de noms Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom    | Choisissez le nom de votre espace de noms |
| Niveau tarifaire | De base |
| Abonnement | Votre abonnement |
| Groupe de ressources | DetectStoppedDevices |
| Lieu | USA Est |
| Unités de débit | 1 |

### <a name="stream-analytics-job"></a>Tâche Stream Analytics

Utilisez le [portail Azure pour créer un travail Stream Analytique](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom    | Choisissez votre nom de la tâche |
| Abonnement | Votre abonnement |
| Groupe de ressources | DetectStoppedDevices |
| Lieu | USA Est |
| Environnement d'hébergement | Cloud |
| Unités de streaming | 3 |

### <a name="function-app"></a>Conteneur de fonctions

Utilisez le [portail Azure pour créer une application de fonction](https://portal.azure.com/#create/Microsoft.FunctionApp) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom de l’application    | Choisissez le nom de votre application (fonction) |
| Abonnement | Votre abonnement |
| Groupe de ressources | DetectStoppedDevices |
| SE | Windows |
| Plan d'hébergement | Plan Consommation |
| Lieu | USA Est |
| Pile d'exécution | .NET |
| Stockage | Création |

### <a name="sendgrid-account"></a>Compte SendGrid

Utilisez le [portail Azure pour créer un compte SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom    | Choisissez le nom de votre compte SendGrid |
| Mot de passe | Créer un mot de passe |
| Abonnement | Votre abonnement |
| Groupe de ressources | DetectStoppedDevices |
| Niveau tarifaire | F1 Gratuit |
| Informations de contact | Remplissez les informations requises |

Lorsque vous avez créé toutes les ressources requises, votre **DetectStoppedDevices** groupe de ressources ressemble à la capture d’écran suivante :

![Détecter le groupe de ressources de périphériques arrêté](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Vous pouvez configurer une application IoT Central exporter en continu des données de télémétrie à un concentrateur d’événements. Dans cette section, vous créez un concentrateur d’événements pour recevoir des données de télémétrie à partir de votre application IoT Central. Le concentrateur d’événements fournit les données de télémétrie à votre travail Stream Analytique pour le traitement.

1. Dans le portail Azure, accédez à votre espace de noms Event Hubs et sélectionnez **+ Event Hub**.
1. Nommez votre concentrateur d’événements **centralexport**, puis sélectionnez **créer**.

Votre espace de noms Event Hubs se présente comme la capture d’écran suivante :

![Espace de noms Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Obtenir la clé d’API SendGrid

Votre application de fonction a besoin d’une clé API SendGrid pour envoyer des messages électroniques. Pour créer une clé API SendGrid :

1. Dans le portail Azure, accédez à votre compte SendGrid. Puis choisissez **gérer** pour accéder à votre compte SendGrid.
1. Dans votre compte SendGrid, choisissez **paramètres**, puis **clés API**. Choisissez **créer une clé API**:

    ![Créer une clé API de SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Sur le **créer une clé API** page, créez une clé nommée **AzureFunctionAccess** avec **un accès complet** autorisations.
1. Prenez note de la clé d’API, vous en avez besoin lorsque vous configurez votre application de fonction.

## <a name="define-the-function"></a>Définir la fonction

Cette solution utilise une application Azure Functions pour envoyer une notification par e-mail lorsque le travail Stream Analytique détecte un périphérique arrêté. Pour créer votre application de fonction :

1. Dans le portail Azure, accédez à la **App Service** d’instance dans le **DetectStoppedDevices** groupe de ressources.
1. Sélectionnez **+** pour créer une nouvelle fonction.
1. Sur le **choisir un environnement de développement A** page, choisissez **dans le portail** , puis sélectionnez **continuer**.
1. Sur le **créer : fonction** page, choisissez **Webhook + API** , puis sélectionnez **créer**.

Le portail crée une fonction par défaut appelée **HttpTrigger1**:

![Fonction de déclenchement HTTP par défaut](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configurer les liaisons de fonction

Pour envoyer des courriers électroniques avec SendGrid, vous devez configurer les liaisons de votre fonction comme suit :

1. Sélectionnez **intégrer**, choisissez la sortie **HTTP ($return)**, puis sélectionnez **supprimer**.
1. Choisissez **+ nouvelle sortie**, puis choisissez **SendGrid**, puis choisissez **sélectionnez**. Choisissez **installer** pour installer l’extension de SendGrid.
1. Lors de l’installation terminée, sélectionnez **utiliser la valeur de retour de fonction**. Ajouter un valide **à l’adresse** pour recevoir des notifications par courrier électronique.  Ajouter un valide **à partir de l’adresse** à utiliser en tant que l’expéditeur de courrier électronique.
1. Sélectionnez **nouveau** regard **paramètre d’application SendGrid API clé**. Entrez **SendGridAPIKey** comme clé et la clé API SendGrid vous avez notée comme valeur. Sélectionnez ensuite **Créer**.
1. Choisissez **enregistrer** pour enregistrer les liaisons SendGrid pour votre fonction.

Les paramètres d’intégration de ressembler à la capture d’écran suivante :

![Intégrations d’application (fonction)](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Ajoutez le code de fonction

Pour implémenter votre fonction, ajoutez le C# code pour analyser la requête HTTP entrante et envoyer les e-mails comme suit :

1. Choisissez le **HttpTrigger1** fonctionner dans votre application de fonction et remplacez le C# code avec le code suivant :

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

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

    Vous pouvez voir un message d’erreur jusqu'à ce que vous enregistriez le nouveau code.

1. Sélectionnez **enregistrer** pour enregistrer la fonction.

### <a name="test-the-function-works"></a>Tester le fonctionnement de la fonction

Pour tester la fonction dans le portail, sélectionnez d’abord **journaux** en bas de l’éditeur de code. Puis choisissez **Test** à droite de l’éditeur de code. Utilisez le JSON suivant en tant que le **corps de la requête**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Les messages de journal (fonction) s’affichent dans le **journaux** panneau :

![Sortie du journal (fonction)](media/howto-create-custom-rules/function-app-logs.png)

Après quelques minutes, le **à** adresse de messagerie reçoit un e-mail avec le contenu suivant :

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Ajouter une requête de Stream Analytique

Cette solution utilise une requête Analytique de Stream pour détecter quand un appareil cesse d’envoyer des données de télémétrie pendant plus de 120 secondes. La requête utilise les données de télémétrie du hub d’événements d’entrée. Le travail envoie les résultats de requête vers l’application de fonction. Dans cette section, vous configurez le travail d’Analytique de Stream :

1. Dans le portail Azure, accédez à votre travail d’analytique de Stream, sous **topologie de travaux** sélectionnez **entrées**, choisissez **+ ajouter une entrée de flux**, puis choisissez **événement Hub**.
1. Utilisez les informations dans le tableau suivant pour configurer l’entrée à l’aide de l’event hub que vous avez créé précédemment, puis choisissez **enregistrer**:

    | Paramètre | Valeur |
    | ------- | ----- |
    | Alias d’entrée | centraltelemetry |
    | Abonnement | Votre abonnement |
    | Espace de noms Event Hub | Votre espace de noms Event Hub |
    | Nom de l’Event Hub | Utiliser l’existant - **centralexport** |

1. Sous **topologie de travaux**, sélectionnez **sorties**, choisissez **+ ajouter**, puis choisissez **fonction Azure**.
1. Utilisez les informations dans le tableau suivant pour configurer la sortie, puis choisissez **enregistrer**:

    | Paramètre | Valeur |
    | ------- | ----- |
    | Alias de sortie | emailnotification |
    | Abonnement | Votre abonnement |
    | Conteneur de fonctions | Votre application de fonction |
    | Fonction  | HttpTrigger1 |

1. Sous **topologie de travaux**, sélectionnez **requête** et remplacez la requête existante par le code SQL suivant :

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
1. Pour démarrer le travail d’Analytique de Stream, choisissez **vue d’ensemble**, puis **Démarrer**, puis **maintenant**, puis **Démarrer**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configurer l’exportation dans IoT Central

Accédez à la [application IoT Central](https://aka.ms/iotcentral) vous avez créé à partir du modèle de Contoso. Dans cette section, vous configurez l’application pour diffuser les données de télémétrie à partir de ses appareils simulés à votre concentrateur d’événements. Pour configurer l’exportation :

1. Accédez à la **exportation de données continue** page, sélectionnez **+ nouveau**, puis **Azure Event Hubs**.
1. Utilisez les paramètres suivants pour configurer l’exportation, puis sélectionnez **enregistrer**:

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom complet  | Exporter vers Event Hubs |
    | activé | Sur |
    | Espace de noms Event Hubs | Nom de votre espace de noms Event Hubs |
    | Event Hub | centralexport |
    | Mesures | Sur |
    | Appareils | Inactif |
    | Modèles d'appareil | Inactif |

![Configuration de l’exportation continue des données](media/howto-create-custom-rules/cde-configuration.png)

Attendez que l’état de l’exportation est **en cours d’exécution** avant de continuer.

## <a name="test"></a>Tester

Pour tester la solution, vous pouvez désactiver l’exportation continue des données à partir de IoT Central pour les appareils simulés arrêtés :

1. Dans votre application IoT Central, accédez à la **exportation de données continue** page et sélectionnez le **exporter vers Event Hubs** exporter la configuration.
1. Définissez **activé** à **hors** et choisissez **enregistrer**.
1. Après au moins deux minutes, le **à** adresse de messagerie reçoit un ou plusieurs des messages électroniques qui ressemblent à l’exemple suivant contenus :

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Nettoyer

Pour nettoyer après cette procédure et éviter les coûts inutiles, supprimez le **DetectStoppedDevices** groupe de ressources dans le portail Azure.

Vous pouvez supprimer l’application IoT Central à partir de la **gestion** page au sein de l’application.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez appris à effectuer les opérations suivantes :

* Données de télémétrie à partir d’une application IoT Central à l’aide de Stream *exportation continue de données*.
* Créer une requête de Stream Analytique qui détecte quand un appareil a arrêté l’envoi des données.
* Envoyer une notification par courrier électronique à l’aide d’Azure Functions et services SendGrid.

Maintenant que vous savez comment créer des règles personnalisées et les notifications, l’étape suivante suggérée consiste à apprendre comment [visualiser et analyser vos données Azure IoT Central dans un tableau de bord Power BI](howto-connect-powerbi.md).
