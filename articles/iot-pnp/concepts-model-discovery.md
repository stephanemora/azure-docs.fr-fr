---
title: Implémenter la découverte de modèles IoT Plug-and-Play (préversion) | Microsoft Docs
description: Cet article destiné aux générateurs de solutions explique comment implémenter la découverte de modèles IoT Plug-and-Play dans une solution.
author: prashmo
ms.author: prashmo
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 364b85a8ead09858b97d5d7e6ca8c130b9960b2c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337379"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implémenter la découverte de modèles IoT Plug-and-Play (préversion) dans une solution IoT

Cet article destiné aux générateurs de solutions explique comment implémenter la découverte de modèles IoT Plug-and-Play (préversion) dans une solution IoT. La découverte de modèles décrit les opérations suivantes :

- Les appareils IoT Plug-and-Play inscrivent leur ID de modèle.
- Une solution IoT récupère les interfaces implémentées par l’appareil.

Il existe deux grandes catégories de solutions IoT :

- Une *solution IoT spécialisée* fonctionne avec un ensemble connu d’interfaces et de modèles d’appareil IoT Plug-and-Play.

- Une *solution IoT pilotée par modèle* peut fonctionner avec n’importe quel appareil IoT Plug-and-Play. Si la création d’une solution pilotée par modèle se révèle plus complexe, ce type de solution présente l’avantage de fonctionner avec tous les appareils qui seront ajoutés à l’avenir.

    Pour générer une solution IoT basée sur un modèle, vous devez créer une logique s’appuyant sur les primitives de l’interface IoT Plug-and-Play : télémétrie, propriétés et commandes. Pour représenter un appareil, la logique de votre solution combine les capacités de plusieurs données de télémétrie, propriétés et commandes.

Cet article explique comment implémenter la découverte de modèles dans les deux types de solutions.

## <a name="model-discovery"></a>Découverte de modèles

Pour découvrir le modèle implémenté par un appareil, une solution peut récupérer l’ID de modèle à l’aide de la découverte basée sur les événements ou sur les jumeaux :

### <a name="event-based-discovery"></a>Découverte basée sur les événements

Lorsqu’un appareil IoT Plug-and-Play se connecte à IoT Hub, il inscrit le modèle qu’il implémente. Cette inscription génère une notification [Événement de modification de jumeau numérique](concepts-digital-twin.md#digital-twin-change-events). Pour savoir comment activer l’acheminement des événements des jumeaux numériques, consultez [Envoi de messages appareil-à-cloud à différents points de terminaison à l’aide de l’acheminement des messages IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

La solution peut utiliser l’événement illustré dans l’extrait de code suivant pour en savoir plus sur l’appareil IoT Plug-and-Play qui se connecte et obtenir son ID de modèle :

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

Cet événement se déclenche lorsque l’ID de modèle d’appareil est ajouté ou mis à jour.

### <a name="twin-based-discovery"></a>Découverte basée sur les jumeaux

Si la solution a besoin de connaître les fonctionnalités d’un appareil donné, elle peut utiliser l’API [Obtenir le jumeau numérique](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin/getdigitaltwin) pour récupérer les informations.

Dans l’extrait de code de jumeau numérique suivant, `$metadata.$model` contient l’ID de modèle d’un appareil IoT Plug-and-Play :

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

La solution peut également utiliser **Obtenir le jumeau** pour récupérer l’ID de modèle auprès du jumeau d’appareil, comme l’illustre l’extrait de code suivant :

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

## <a name="model-resolution"></a>Résolution de modèle

Les solutions utilisent la résolution de modèle pour obtenir l’accès aux interfaces qui composent un modèle à partir de l’ID de modèle. 

- Elles peuvent choisir de stocker ces interfaces sous forme de fichiers dans un dossier local. 
- Elles peuvent utiliser le [référentiel de modèles](concepts-model-repository.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez plus sur la découverte de modèles dans une solution IoT, découvrez la [plateforme Azure IoT](overview-iot-plug-and-play.md) pour utiliser d’autres fonctionnalités pour votre solution.

- [Interaction avec un appareil à partir de la solution](quickstart-service-node.md)
- [API REST de jumeau numérique IoT](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Explorateur Azure IoT](howto-use-iot-explorer.md)
