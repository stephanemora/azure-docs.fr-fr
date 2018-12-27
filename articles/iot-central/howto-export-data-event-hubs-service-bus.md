---
title: Exporter vos données vers Azure Event Hubs et Azure Service Bus | Microsoft Docs
description: Comment exporter des données depuis votre application Azure IoT Central vers Azure Event Hubs et Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318401"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exporter vos données dans Azure IoT Central

*Cette rubrique s’applique aux administrateurs.*

Cet article étudie en profondeur la façon d’utiliser la fonctionnalité d’exportation continue de données d’Azure IoT Central afin d’exporter vos données vers vos instances **Azure Event Hubs** et **Azure Service Bus**. Vous pouvez exporter des **mesures**, des **appareils** et des **modèles d’appareil** vers votre propre destination pour obtenir des informations et des analyses sur le chemin à chaud. Cela inclut le déclenchement de règles personnalisées dans Azure Stream Analytics, le déclenchement de workflows personnalisés dans Azure Logic Apps ou la transformation des données et leur transmission via Azure Functions. 

> [!Note]
> Une fois encore, quand vous activez l’exportation de données continue, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données continue est désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données continue tôt dans le processus.


## <a name="prerequisites"></a>Prérequis

- Vous devez être administrateur dans votre application IoT Central

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Exporter vers Azure Event Hubs et Azure Service Bus

Les données de mesures, d’appareils et de modèles d’appareil sont exportées en temps quasi réel vers votre event Hub ou la file d’attente ou la rubrique Service Bus. Les données de mesures exportées contiennent l’intégralité du message que vos appareils ont envoyé à IoT Central, pas uniquement les valeurs des mesures elles-mêmes. Les données des appareils exportés contiennent les modifications apportées aux propriétés et aux paramètres de tous les appareils, et les modèles d’appareil exportés contiennent les modifications apportées à tous les modèles d’appareil. Les données exportées résident dans la propriété « body » au format JSON.

> [!NOTE]
> Lors du choix d’un Service Bus comme destination d’exportation, **les sessions et la détection des doublons ne doivent pas être activés** dans les files d’attente et les rubriques. Si l’une de ces options est activée, certains messages n’arriveront pas dans votre file d’attente ou votre rubrique.

### <a name="measurements"></a>Mesures

Un nouveau message est exporté rapidement après que IoT Central a reçu le message d’un appareil. Chaque message exporté dans Event Hubs et Service Bus contient le message complet envoyé par l’appareil dans la propriété « body » au format JSON. 

> [!NOTE]
> Les appareils qui envoient les mesures sont représentés par leur ID d’appareil (voir les sections suivantes). Pour obtenir les noms des appareils, exportez les données de ceux-ci et corrélez chaque message en utilisant le **connectionDeviceId** qui correspond au **deviceId** du message de l’appareil.

L’exemple suivant montre un message concernant les données de mesure reçues dans la file d’attente ou le sujet de l’event hub ou de Service Bus.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Appareils

Les messages contenant les données d’appareils sont envoyés à votre event Hub ou à la file d’attente ou à la rubrique Service Bus une fois toutes les n minutes. Cela signifie que toutes les quelques minutes, un lot de messages arrivera avec des données sur
- Nouveaux appareils qui ont été ajoutés
- Appareils pour lesquels des valeurs de propriétés et de paramètres ont changé

Chaque message représente un ou plusieurs changements apportés à un appareil depuis le dernier message exporté. Les informations qui seront envoyées dans chaque message incluent :
- `id` de l’appareil dans IoT Central
- `name` de l’appareil
- `deviceId` issu du [service de provisionnement des appareils](https://aka.ms/iotcentraldocsdps)
- Informations sur le modèle d’appareil
- Valeurs de propriétés
- Valeurs de paramètres

> [!NOTE]
> Les appareils supprimés depuis le dernier lot ne sont pas exportés. Actuellement, il n’y a aucun indicateur dans les messages exportés pour les appareils supprimés.
>
> Le modèle d’appareil auquel chaque appareil appartient est représenté par un ID de modèle d’appareil. Pour obtenir le nom du modèle d’appareil, veillez à exporter les données des modèles d’appareil également.

L’exemple suivant montre un message concernant les données d’appareil dans la file d’attente ou le sujet de l’event Hub ou de Service Bus :


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Modèles d’appareil

Les messages contenant les données des modèles d’appareils sont envoyés à votre event Hub ou à la file d’attente ou à la rubrique Service Bus une fois toutes les n minutes. Cela signifie que toutes les quelques minutes, un lot de messages arrivera avec des données sur
- Nouveaux modèles d’appareil qui ont été ajoutés
- Modèles d’appareil pour lesquels les définitions de mesures, propriétés et paramètres ont changé

Chaque message représente un ou plusieurs changements apportés à un modèle d’appareil depuis le dernier message exporté. Les informations qui seront envoyées dans chaque message incluent :
- `id` du modèle d’appareil
- `name` du modèle d’appareil
- `version` du modèle d’appareil
- Types de données et valeurs minimales/maximales des mesures
- Types de données et valeurs par défaut des propriétés
- Types de données et valeurs par défaut des paramètres

> [!NOTE]
> Les modèles d’appareil supprimés depuis le dernier lot ne sont pas exportés. Actuellement, il n’y a aucun indicateur dans les messages exportés pour les modèles d’appareil supprimé.

L’exemple suivant montre un message concernant les données des modèles d’appareil dans la file d’attente ou le sujet de l’event Hub ou de Service Bus :

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment exporter vos données vers Azure Event Hubs et Azure Service Bus, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Comment déclencher Azure Functions](howto-trigger-azure-functions.md)
