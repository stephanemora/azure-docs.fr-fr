---
title: Azure IoT Hub et Event Grid | Microsoft Docs
description: Azure Event Grid permet de déclencher des processus en fonction d’actions qui se produisent dans IoT Hub.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: f08845dbf4168627d0198e81d2092a1fe56c6c89
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733865"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Réagir aux événements IoT Hub en utilisant Event Grid pour déclencher des actions

Azure IoT Hub s’intègre à Azure Event Grid pour vous permettre d’envoyer des notifications d’événements à d’autres services et de déclencher des processus en aval. Configurez vos applications métier pour écouter les événements IoT Hub, afin de pouvoir réagir aux événements critiques de manière fiable, scalable et sécurisée. Par exemple, créer une application qui met à jour une base de données, crée un ticket de travail et remet une notification par e-mail chaque fois qu’un nouvel appareil IoT est inscrit à votre IoT hub.

[Azure Event Grid](../event-grid/overview.md) est un service de routage d’événement entièrement géré qui utilise le modèle publication-abonnement. Event Grid offre une prise en charge intégrée des services Azure comme [Azure Functions](../azure-functions/functions-overview.md) et [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), et peut fournir des alertes d’événements à des services non-Azure à l’aide de webhooks. Pour obtenir une liste complète des gestionnaires d’événements qui prennent en charge Event Grid, consultez [Présentation d’Azure Event Grid](../event-grid/overview.md).

![Architecture d’Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilité régionale

L’intégration Event Grid est disponible pour les hubs IoT situés dans les régions où Event Grid est pris en charge. Tous les événements de périphérique à l’exception des événements de télémétrie d’appareil sont généralement disponibles. Événement de télémétrie d’appareil est en version préliminaire publique et est disponible dans toutes les régions à l’exception des États-Unis, ouest des États-Unis, Europe de l’ouest, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china/china-welcome), et [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/). Pour consulter la dernière liste des régions, reportez-vous à [Présentation d’Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Types d’événements

IoT Hub publie les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publié quand un appareil est inscrit auprès d’un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publié quand un appareil est supprimé d’un hub IoT. |
| Microsoft.Devices.DeviceConnected | Publié quand un appareil est connecté à un hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publié quand un appareil est déconnecté d’un hub IoT. |
| Microsoft.Devices.DeviceTelemetry | Publié lorsqu’un message de télémétrie d’appareil est envoyé à un hub IoT |

Utilisez le portail Azure ou Azure CLI pour configurer les événements à publier à partir de chaque hub IoT. Pour obtenir un exemple, suivez le didacticiel [Envoyer des notifications par e-mail sur des événements Azure IoT Hub à l’aide de Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schéma d’événement

Les événements IoT Hub contiennent toutes les informations dont vous avez besoin pour répondre aux modifications du cycle de vie de vos appareils. Vous pouvez identifier un événement IoT Hub en vérifiant que la propriété eventType commence par **Microsoft.Devices**. Pour plus d’informations sur la façon d’utiliser des propriétés d’événements Event Grid, consultez le [Schéma d’événement Azure Event Grid](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schéma d’appareil connecté

L’exemple suivant montre le schéma d’un événement de connexion d’appareil :

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Schéma de données de télémétrie d’appareil

Message de télémétrie d’appareil doit être au format JSON valid avec le contentType défini sur JSON et contentEncoding défini sur UTF-8 dans le message [propriétés système](iot-hub-devguide-routing-query-syntax.md#system-properties). S’il n’est pas défini, IoT Hub écrire les messages dans un format de codé en base 64.

Vous pouvez enrichir les événements de télémétrie d’appareil avant d’être publiées en sélectionnant le point de terminaison sous forme de grille d’événements à Event Grid. Pour plus d’informations, consultez [vue d’ensemble des enrichissements de Message](iot-hub-message-enrichments-overview.md).

L’exemple suivant montre le schéma d’un événement de télémétrie d’appareil :

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Schéma de création d’appareil

L’exemple suivant montre le schéma d’un événement de création d’appareil :

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Pour obtenir une description détaillée de chaque propriété, consultez [schéma d’événement Azure Event Grid pour IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrer les événements

Les abonnements aux événements IoT Hub peuvent filtrer les événements basés sur le type d’événement, contenu de données et l’objet, qui est le nom de l’appareil.

Event Grid permet [filtrage](../event-grid/event-filtering.md) sur le contenu de types, les sujets et les données d’événement. Lors de la création de l’abonnement Event Grid, vous pouvez choisir pour vous abonner aux événements IoT sélectionnés. Les filtres d’objets dans Event Grid fonctionnent d’après des correspondances de **Commence avec** (préfixe) et de **Se termine par** (suffixe). Le filtre utilise un opérateur `AND`. Ainsi, les événements avec un objet qui correspond à la fois au préfixe et au suffixe sont remis à l’abonné.

L’objet des événements IoT utilise le format suivant :

```json
devices/{deviceId}
```

Event Grid permet également le filtrage sur les attributs de chaque événement, y compris le contenu des données. Cela vous permet de choisir quels événements sont remis en contenu du message de télémétrie. Consultez [filtrage avancé](../event-grid/event-filtering.md#advanced-filtering) pour consulter des exemples.

Pour les événements de données de télémétrie non comme DeviceConnected, DeviceDisconnected, DeviceCreated et DeviceDeleted, le filtrage d’Event Grid peut être utilisé lors de la création de l’abonnement. Pour les événements de télémétrie, outre le filtrage dans Event Grid, les utilisateurs peuvent également filtrer sur jumeaux d’appareil, les propriétés de message et les corps de la requête de routage du message. Nous créons une valeur par défaut [itinéraire](iot-hub-devguide-messages-d2c.md) dans IoT Hub, selon votre abonnement Event Grid pour la télémétrie d’appareil. Cet itinéraire unique peut gérer tous vos abonnements Event Grid. Pour filtrer les messages avant l’envoi de données de télémétrie, vous pouvez mettre à jour votre [requête routage](iot-hub-devguide-routing-query-syntax.md). Notez que cette requête de routage peut être appliquée au corps du message uniquement si le corps est JSON.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitations pour les événements d’état de la connexion et de la déconnexion d’appareils

Pour recevoir les événements de connexion et de déconnexion d’appareils, vous devez ouvrir le lien D2C ou C2D pour votre appareil. Si votre appareil utilise le protocole MQTT, IoT Hub gardera le lien C2D ouvert. Pour AMQP, vous pouvez ouvrir le lien C2D en appelant le [recevoir les API asynchrones](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Le lien D2C est ouvert si vous envoyez des données de télémétrie. Si la connexion de l’appareil est scintillement, ce qui signifie que l’appareil se connecte et se déconnecte fréquemment, nous n’enverront pas de chaque état de la connexion unique, mais publie l’état de connexion dont un instantané est réalisé toutes les minutes. En cas de panne de IoT Hub, nous publierons l’état de connexion d’appareil une fois celle-ci terminée. Si l’appareil se déconnecte pendant la panne, l’événement de déconnexion d’appareil est publié dans les 10 minutes.

## <a name="tips-for-consuming-events"></a>Conseils relatifs à la consommation d’événements

Les applications qui gèrent des événements IoT Hub doivent suivre ces pratiques suggérées :

* Plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements, ne déduisez que les événements proviennent d’une source particulière. Vérifiez toujours la rubrique du message pour être sûr qu’il provient du hub IoT prévu.

* Ne supposez pas que tous les événements reçus sont des types attendus. Vérifiez toujours eventType avant de traiter le message.

* Les messages peuvent arriver dans le désordre ou après un certain délai. Utilisez le champ etag pour comprendre si vos informations sur les objets sont à jour pour l’appareil créé ou des événements d’appareil supprimé.

## <a name="next-steps"></a>Étapes suivantes

* [Suivre le didacticiel d’événements IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Apprendre à commander des événements d’état de la connexion et de la déconnexion d’appareils](iot-hub-how-to-order-connection-state-events.md)

* [En savoir plus sur Event Grid](../event-grid/overview.md)

* [Comparer les différences entre le routage des messages et des événements IoT Hub](iot-hub-event-grid-routing-comparison.md)
