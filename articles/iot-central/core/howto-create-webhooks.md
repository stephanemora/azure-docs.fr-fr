---
title: Créer des webhooks sur des règles dans Azure IoT Central | Microsoft Docs
description: Créer des webhooks dans Azure IoT Central pour informer automatiquement les autres applications lorsque des règles se déclenchent.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158093"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Créer des actions de webhook sur des règles dans Azure IoT Central

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

Les webhooks vous permettent de connecter votre application IoT Central à d’autres applications et services à des fins de surveillance à distance et de notification. Les webhooks informent automatiquement les autres applications et services connectés dès lors qu’une règle est déclenchée dans votre application IoT Central. Votre application IoT Central envoie une requête POST à l’autre point de terminaison HTTP de l’application chaque fois qu’une règle est déclenchée. La charge utile contient les détails de l’appareil et les détails du déclencheur de la règle.

## <a name="set-up-the-webhook"></a>Configurer le webhook

Dans cet exemple, vous vous connectez au point de terminaison RequestBin pour être averti quand des règles sont déclenchées en utilisant des webhooks.

1. Ouvrez [RequestBin](https://requestbin.net/).

1. Créez un point de terminaison RequestBin et copiez **l’URL du fichier Bin**.

1. Créez une [règle de télémétrie](tutorial-create-telemetry-rules.md). Enregistrez la règle et ajoutez une nouvelle action.

    ![Écran de création d’un webhook](media/howto-create-webhooks/webhookcreate.png)

1. Choisissez l’action de webhook et indiquez un nom d’affichage, puis collez l’URL du fichier Bin en tant qu’URL de rappel.

1. Enregistrez la règle.

Quand la règle est déclenchée, une nouvelle requête apparaît dans RequestBin.

## <a name="payload"></a>Payload

Quand une règle est déclenchée, une requête HTTP POST est envoyée à l’URL de rappel contenant une charge utile json avec les détails de la télémétrie, de l’appareil, de la règle et de l’application. La charge utile peut ressembler à ceci :

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Limitations connues

Actuellement, il n’existe aucun moyen de s’abonner/se désabonner de ces webhooks via une API de programmation.

Si vous avez des idées pour améliorer cette fonctionnalité, publiez vos suggestions sur notre [forum Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez découvert comment configurer et utiliser des webhooks, l’étape suivante suggérée est de découvrir la [configuration des Groupes d’actions Azure Monitor](howto-use-action-groups.md).
