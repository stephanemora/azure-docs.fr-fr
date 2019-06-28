---
title: Exécuter plusieurs actions à partir d’une règle Azure IoT Central | Microsoft Docs
description: Exécutez plusieurs actions à partir d’une même règle IoT Central et créez des groupes d’actions réutilisables que vous pouvez exécuter à partir de plusieurs règles.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60517219"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Regrouper plusieurs actions à exécuter à partir d’une ou plusieurs règles

*Cet article s’applique aux créateurs et aux administrateurs.*

Dans Azure IoT Central, vous créez des règles pour exécuter des actions quand une condition est satisfaite. Les règles sont basées sur la télémétrie des appareils ou sur des événements. Par exemple, vous pouvez avertir un opérateur quand la température dans un appareil dépasse un certain seuil. Cet article décrit comment utiliser des *groupes d’actions* [Azure Monitor](../azure-monitor/overview.md) pour attacher plusieurs actions à une règle IoT Central. Vous pouvez attacher un groupe d’actions à plusieurs règles. Un [groupe d’actions](../azure-monitor/platform/action-groups.md) est une collection de préférences de notification définies par le propriétaire d’un abonnement Azure.

## <a name="prerequisites"></a>Prérequis

- Application avec paiement à l'utilisation
- Un compte et un abonnement Azure pour créer et gérer des groupes d’actions Azure Monitor

## <a name="create-action-groups"></a>Créer des groupes d’actions

Vous pouvez [créer et gérer des groupes d’actions dans le portail Azure](../azure-monitor/platform/action-groups.md) ou avec un [modèle Azure Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Un groupe d’actions peut :

- Envoyer des notifications, comme un e-mail, un SMS, ou passer un appel vocal.
- Exécuter une action, comme appeler un webhook.

La capture d’écran suivante montre un groupe d’actions qui envoie des notifications par e-mail et par SMS, et qui appelle un webhook :

![Groupe d’actions](media/howto-use-action-groups/actiongroup.png)

Pour utiliser un groupe d’actions dans une règle IoT Central, le groupe d’actions doit être dans le même abonnement Azure que l’application IoT Central.

## <a name="use-an-action-group"></a>Utiliser un groupe d’actions

Pour utiliser un groupe d’actions dans votre application IoT Central, créez d’abord une règle de télémétrie ou d’événement. Quand vous ajoutez une action à la règle, sélectionnez **Groupes d’actions Azure Monitor** :

![Choisir une action](media/howto-use-action-groups/chooseaction.png)

Choisissez un groupe d’actions dans votre abonnement Azure :

![Choisir un groupe d’actions](media/howto-use-action-groups/chooseactiongroup.png)

Sélectionnez **Enregistrer**. Le groupe d’actions apparaît maintenant dans la liste des actions à exécuter quand la règle est déclenchée :

![Groupe d’actions enregistré](media/howto-use-action-groups/savedactiongroup.png)

Le tableau suivant récapitule les informations envoyées aux types d’actions pris en charge :

| Type d’action | Format de sortie |
| ----------- | -------------- |
| Email       | Modèle d’e-mail standard IoT Central |
| sms         | Alerte Azure IoT Central : ${nom_application} - « ${nom_règle} » déclenchée sur « ${nom_appareil} » le ${date_déclenchement} ${date_déclenchement} |
| Voix       | Alerte Azure I.O.T Central : règle « ${nom_règle} » déclenchée sur l’appareil « ${nom_appareil} » le ${date_déclenchement} ${date_déclenchement}, dans l’application ${nom_application} |
| webhook     | { « schemaId » : « AzureIoTCentralRuleWebhook », « données » : {[charge utile de webhook régulier](#payload)} } |

Le texte suivant est un exemple de message SMS provenant d’un groupe d’actions :

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Le JSON suivant montre une exemple de charge utile d’actions de webhook :

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

Maintenant que vous avez découvert comment utiliser des groupes d’actions avec des règles, l’étape suivante suggérée est de découvrir comment [gérer vos appareils](howto-manage-devices.md).
