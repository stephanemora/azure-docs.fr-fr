---
title: Créer des webhooks sur des règles dans Azure IoT Central | Microsoft Docs
description: Créer des webhooks dans Azure IoT Central pour informer automatiquement les autres applications lorsque des règles se déclenchent.
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 62599419d5634bea7cd25c93fbada8b472b95c4d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771941"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Créer des actions de webhook sur des règles dans Azure IoT Central

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

Les webhooks vous permettent de connecter votre application IoT Central à d’autres applications et services à des fins de surveillance à distance et de notification. Les webhooks informent automatiquement les autres applications et services connectés dès lors qu’une règle est déclenchée dans votre application IoT Central. Votre application IoT Central enverra une requête POST au point de terminaison HTTP de l’autre application chaque fois qu’une règle est déclenchée. La charge utile contiendra les détails de l’appareil et les détails du déclencheur de la règle. 

## <a name="how-to-set-up-the-webhook"></a>Comment configurer le webhook
Dans cet exemple, vous allez vous connecter au point de terminaison RequestBin pour être averti dès lors que des règles sont déclenchées à l’aide de webhooks. 

1. Ouvrez [RequestBin](http://requestbin.net/). 
1. Créez un point de terminaison RequestBin et copiez **l’URL du fichier Bin**. 
1. Créez une [règle de télémétrie](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ou une [règle d’événement](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Enregistrez la règle et ajoutez une nouvelle action.
    ![Écran de création de webhook](media/howto-create-webhooks-experimental/webhookcreate.png)
1. Choisissez l’action de webhook et indiquez un nom d’affichage, puis collez l’URL du fichier Bin en tant qu’URL de rappel. 
1. Enregistrez la règle.

Lorsque la règle se déclenche, une nouvelle requête s’affiche dans RequestBin.

## <a name="payload"></a>Payload
Lorsqu’une règle est déclenchée, une requête HTTP POST est envoyée à l’URL de rappel contenant une charge utile json avec des mesures et les détails de l’appareil, de la règle et de l’application. Pour une règle de télémétrie, la charge utile peut ressembler à ce qui suit :

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
Maintenant que vous avez appris à configurer et utiliser des webhooks, nous vous suggérons de découvrir comment [créer des workflows dans Microsoft Flow](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
