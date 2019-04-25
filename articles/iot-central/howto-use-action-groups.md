---
title: Exécuter plusieurs actions à partir d’une règle d’Azure IoT Central | Microsoft Docs
description: Exécuter plusieurs actions à partir d’une seule règle IoT Central et créer des groupes d’actions que vous pouvez exécuter à partir de plusieurs règles réutilisables.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517219"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Plusieurs actions à exécuter à partir d’une ou plusieurs règles de groupe

*Cet article s’applique aux générateurs et aux administrateurs.*

Dans Azure IoT Central, vous créez des règles pour exécuter des actions lorsqu’une condition est remplie. Règles sont basées sur la télémétrie d’appareil ou des événements. Par exemple, vous pouvez avertir un opérateur lorsque la température dans un appareil dépasse un seuil. Cet article décrit comment utiliser [Azure Monitor](../azure-monitor/overview.md) *groupes d’actions* pour associer plusieurs actions à une règle de IoT Central. Vous pouvez joindre un groupe d’actions à plusieurs règles. Un [groupe d’actions](../azure-monitor/platform/action-groups.md) est une collection de préférences de notification définie par le propriétaire d’un abonnement Azure.

## <a name="prerequisites"></a>Conditions préalables

- Application avec paiement à l'utilisation
- Un compte et abonnement Azure pour créer et gérer des groupes d’actions Azure Monitor

## <a name="create-action-groups"></a>Créer des groupes d’actions

Vous pouvez [créer et gérer des groupes d’actions dans le portail Azure](../azure-monitor/platform/action-groups.md) ou avec un [modèle Azure Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Un groupe d’actions peut :

- Envoyer des notifications par exemple, un e-mail, SMS, ou passer un appel vocal.
- Exécuter une action telle que l’appel d’un webhook.

La capture d’écran suivante montre un groupe d’actions qui envoie des e-mails et des notifications par SMS et appelle un webhook :

![Groupe d’actions](media/howto-use-action-groups/actiongroup.png)

Pour utiliser un groupe d’actions dans une règle de IoT Central, le groupe d’actions doit être dans le même abonnement Azure en tant que l’application IoT Central.

## <a name="use-an-action-group"></a>Utiliser un groupe d’actions

Pour utiliser un groupe d’actions dans votre application IoT Central, tout d’abord créer une règle de télémétrie ou d’événement. Lorsque vous ajoutez une action à la règle, sélectionnez **groupes d’actions Azure Monitor**:

![Choisir une action](media/howto-use-action-groups/chooseaction.png)

Choisissez un groupe d’actions à partir de votre abonnement Azure :

![Choisissez le groupe d’actions](media/howto-use-action-groups/chooseactiongroup.png)

Sélectionnez **Enregistrer**. Le groupe d’actions apparaît maintenant dans la liste des actions à exécuter lorsque la règle est déclenchée :

![Enregistrer le groupe d’actions](media/howto-use-action-groups/savedactiongroup.png)

Le tableau suivant récapitule les informations envoyées pour les types d’actions prises en charge :

| Type d’action | Format de sortie |
| ----------- | -------------- |
| Email       | Modèle de message électronique standard IoT Central |
| sms         | Alerte Azure IoT Central : ${applicationName} - « ${ruleName} » déclenchée sur « ${deviceName} » à ${triggerDate}, ${triggerTime} |
| Voix       | Alerte I.O.T Central Azure : règle de « ${ruleName} » déclenchée sur l’appareil « ${deviceName} » à ${triggerDate}, ${triggerTime}, de l’application ${applicationName} |
| webhook     | { "schemaId" : « AzureIoTCentralRuleWebhook », « données » : {[charge utile du webhook régulière](#payload)}} |

Le texte suivant est un exemple de message SMS à partir d’un groupe d’actions :

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Le JSON suivant illustre une exemple webhook action de charge utile :

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser des groupes d’actions avec des règles, l’étape suivante suggérée consiste à apprendre comment [gérer vos appareils](howto-manage-devices.md).
