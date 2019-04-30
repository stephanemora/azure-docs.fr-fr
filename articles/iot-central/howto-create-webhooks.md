---
title: Créer des webhooks sur des règles dans Azure IoT Central | Microsoft Docs
description: Créer des webhooks dans Azure IoT Central pour informer automatiquement les autres applications lorsque des règles se déclenchent.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 22167de6676837c45c48a0bafd19b1ba69578827
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888311"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Créer des actions de webhook sur des règles dans Azure IoT Central

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

Les webhooks vous permettent de connecter votre application IoT Central à d’autres applications et services à des fins de surveillance à distance et de notification. Les webhooks informent automatiquement les autres applications et services connectés dès lors qu’une règle est déclenchée dans votre application IoT Central. Votre application IoT Central envoie une demande POST au point de terminaison HTTP de l’autre application chaque fois qu’une règle est déclenchée. La charge utile contient les détails de l’appareil et les informations de déclencheur de règle.

## <a name="set-up-the-webhook"></a>Configurer le webhook

Dans cet exemple, vous vous connectez à un élément RequestBin pour être averti lorsque les règles se déclenchent à l’aide de webhooks.

1. Ouvrez [RequestBin](https://requestbin.net/).

1. Créez un point de terminaison RequestBin et copiez **l’URL du fichier Bin**.

1. Créez une [règle de télémétrie](howto-create-telemetry-rules.md) ou une [règle d’événement](howto-create-event-rules.md). Enregistrez la règle et ajoutez une nouvelle action.

    ![Écran de création de Webhook](media/howto-create-webhooks/webhookcreate.png)

1. Choisissez l’action de webhook et indiquez un nom d’affichage, puis collez l’URL du fichier Bin en tant qu’URL de rappel.

1. Enregistrez la règle.

Maintenant lorsque la règle est déclenchée, vous voyez une nouvelle demande d’apparaissent dans RequestBin.

## <a name="payload"></a>Payload

Lorsqu’une règle est déclenchée, une requête HTTP POST est envoyée à l’URL de rappel contenant une charge utile json avec des mesures et les détails de l’appareil, de la règle et de l’application. Pour une règle de télémétrie, la charge utile de se présente comme suit :

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Limites connues

Actuellement, il n’existe aucun moyen de s’abonner/se désabonner de ces webhooks via une API de programmation.

Si vous avez des idées pour améliorer cette fonctionnalité, publiez vos suggestions sur notre [forum Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer et utiliser des webhooks, l’étape suivante suggérée consiste à Explorer [création de workflows dans Microsoft Flow](howto-add-microsoft-flow.md).
