---
title: Configurer des règles et des actions dans Azure IoT Central
description: Dans le cadre de cet article de guide pratique, vous découvrez, en tant que générateur, comment configurer des règles et des actions basées sur la télémétrie dans votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5d36c303f1ebc23b317ccd5dcb08d65f5ceaf30b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563059"
---
# <a name="configure-rules"></a>Configurer des règles

Dans IoT Central, les règles servent d’outil de réponse personnalisable qui déclenche des événements faisant l’objet d’une supervision active à partir d’appareils connectés. Les sections suivantes expliquent comment les règles sont évaluées. Vous pouvez définir une ou plusieurs actions qui se produisent lorsqu’une règle se déclenche : cet article décrit les groupes d’actions de messagerie, de webhook et Azure Monitor. Pour en savoir plus sur les autres types d’action, consultez [Utiliser des workflows pour intégrer votre application Azure IoT Central à d’autres services cloud](howto-configure-rules-advanced.md).

## <a name="select-target-devices"></a>Sélectionner les appareils cibles

Utilisez la section Appareils cibles pour sélectionner le type d’appareils auxquels cette règle sera appliquée. Des filtres vous permettent d’affiner davantage les appareils à inclure. Les filtres utilisent des propriétés sur le modèle d’appareil pour filtrer l’ensemble d’appareils. Les filtres ne déclenchent pas eux-mêmes une action. Dans la capture d’écran suivante, les appareils ciblés sont de type de modèle d’appareil **Refrigerator** (Réfrigérateur). Le filtre indique que la règle doit inclure uniquement les réfrigérateurs (**Refrigerator**) où la propriété **Manufactured State** (État de fabrication) a la valeur **Washington**.

:::image type="content" source="media/howto-configure-rules/filters.png" alt-text="Capture d’écran montrant une définition de condition.":::

## <a name="use-multiple-conditions"></a>Utiliser plusieurs conditions

Les conditions déterminent ce qui entraîne le déclenchement des règles. Vous pouvez ajouter plusieurs conditions à une règle et spécifier si la règle doit se déclencher lorsque toutes les conditions sont vraies ou lorsque l’une d’entre elles est vraie.  

Dans la capture d’écran suivante, les conditions vérifient que la température est supérieure à 70 &deg;F et que l’humidité est inférieure à 10. Lorsque l’une de ces instructions est vraie, la règle prend la valeur true et déclenche une action.

:::image type="content" source="media/howto-configure-rules/conditions.png" alt-text="La capture d’écran représente un moniteur de réfrigérateur avec des conditions spécifiées pour la température et l’humidité.":::

> [!NOTE]
> Actuellement, seules les conditions de télémétrie sont prises en charge.  

### <a name="use-a-cloud-property-in-a-value-field"></a>Utiliser une propriété Cloud dans un champ de valeur

Vous pouvez référencer une propriété de Cloud du modèle d’appareil dans le champ **Valeur** pour une condition. La propriété de Cloud et la valeur de télémétrie doivent être de types similaires. Par exemple, si **Température** est un double, seules les propriétés Cloud de type double s’affichent en tant qu’options dans la liste déroulante **Valeur**.

Si vous choisissez une valeur de télémétrie de type d’événement, la liste déroulante **Valeur** comprend l’option **Toute**. L’option **Toute** signifie que la règle se déclenche quand votre application reçoit un événement de ce type, quelle que soit la charge utile.

## <a name="use-aggregate-windowing"></a>Utiliser le fenêtrage des temps d’agrégation

Vous pouvez spécifier une agrégation de temps pour déclencher votre règle en fonction d’une fenêtre de temps. Les conditions de règle évaluent les fenêtres de temps d’agrégation sur les données de télémétrie comme des fenêtres bascule. Si la règle contient des filtres de propriété, ceux-ci sont appliqués à la fin de la fenêtre de temps. Dans la capture d’écran ci-dessous, la fenêtre de temps est de cinq minutes. Toutes les cinq minutes, la règle évalue les cinq dernières minutes de données télémétriques. Les données ne sont évaluées qu’une seule fois dans la fenêtre à laquelle elles correspondent.

:::image type="content" source="media/howto-configure-rules/tumbling-window.png" alt-text="Diagramme montrant la définition des fenêtres bascule.":::

## <a name="create-an-email-action"></a>Créer une action de messagerie

Lorsque vous créez une action de messagerie, l’adresse e-mail doit être un **ID d’utilisateur** dans l’application, et l’utilisateur doit s’être connecté à l’application au moins une fois. Vous pouvez également spécifier une note à inclure dans l’e-mail. IoT Central montre un exemple de ce à quoi ressemblera l’e-mail lorsque la règle se déclenchera :

:::image type="content" source="media/howto-configure-rules/email-action.png" alt-text="Capture d’écran montrant une action de messagerie pour une règle.":::

## <a name="create-a-webhook-action"></a>Créer une action de webhook

Les webhooks vous permettent de connecter votre application IoT Central à d’autres applications et services à des fins de surveillance à distance et de notification. Les webhooks informent automatiquement les autres applications et services connectés dès lors qu’une règle est déclenchée dans votre application IoT Central. Votre application IoT Central envoie une requête POST à l’autre point de terminaison HTTP de l’application chaque fois qu’une règle se déclenche. La charge utile contient les détails de l’appareil et les détails du déclencheur de la règle.

Dans cet exemple, vous vous connectez au point de terminaison *RequestBin* pour être informé du déclenchement d’une règle :

1. Ouvrez [RequestBin](https://requestbin.net/).

1. Créez un point de terminaison RequestBin et copiez **l’URL du fichier Bin**.

1. Ajoutez une action à votre règle :

    :::image type="content" source="media/howto-configure-rules/webhook-create.png" alt-text="Capture d’écran montrant l’écran de création du webhook.":::

1. Choisissez l’action de webhook, entrez un nom d’affichage et collez l’URL de RequestBin comme **URL de rappel**.

1. Enregistrez la règle.

Maintenant, lorsque la règle se déclenche, une nouvelle requête apparaît dans RequestBin.

### <a name="payload"></a>Payload

Lorsqu’une règle se déclenche, elle envoie une requête HTTP POST à l’URL de rappel. La requête contient une charge utile JSON avec les détails de la télémétrie, de l’appareil, de la règle et de l’application. La charge utile ressemble à l’extrait de code JSON suivant :

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```

Si la règle supervise la télémétrie agrégée sur une période donnée, la charge utile contient une section de télémétrie qui ressemble à ceci :

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

### <a name="data-format-change-notice"></a>Avis de modification du format de données

Si vous avez créé et enregistré un ou plusieurs webhooks avant le **3 avril 2020**, supprimez-les et créez-en d’autres. Les webhooks plus anciens utilisent un format de charge utile déconseillé :

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
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="create-an-azure-monitor-group-action"></a>Créer un groupe d’actions Azure Monitor

Cette section explique comment utiliser des *groupes d’actions* [Azure Monitor](../../azure-monitor/overview.md) pour attacher plusieurs actions à une règle IoT Central. Vous pouvez attacher un groupe d’actions à plusieurs règles. Un [groupe d’actions](../../azure-monitor/alerts/action-groups.md) est une collection de préférences de notification définies par le propriétaire d’un abonnement Azure.

Vous pouvez [créer et gérer des groupes d’actions dans le portail Azure](../../azure-monitor/alerts/action-groups.md) ou avec un [modèle Azure Resource Manager](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md).

Un groupe d’actions peut :

- Envoyer des notifications, comme un e-mail, un SMS, ou passer un appel vocal.
- Exécuter une action, comme appeler un webhook.

La capture d’écran suivante montre un groupe d’actions qui envoie des notifications par e-mail et par SMS, et qui appelle un webhook :

:::image type="content" source="media/howto-configure-rules/action-group.png" alt-text="Capture d’écran montrant un groupe d’actions dans le portail Azure.":::

Pour utiliser un groupe d’actions dans une règle IoT Central, le groupe d’actions doit être dans le même abonnement Azure que l’application IoT Central.

Quand vous ajoutez une action à la règle dans IoT Central, sélectionnez **Groupes d’actions Azure Monitor**.

Choisissez un groupe d’actions dans votre abonnement Azure :

:::image type="content" source="media/howto-configure-rules/choose-action-group.png" alt-text="Capture d’écran montrant un groupe d’actions dans une règle IoT Central.":::

Sélectionnez **Enregistrer**. Le groupe d’actions apparaît maintenant dans la liste des actions à exécuter lorsque la règle est déclenchée.

Le tableau suivant récapitule les informations envoyées aux types d’actions pris en charge :

| Type d'action | Format de sortie |
| ----------- | -------------- |
| E-mail       | Modèle d’e-mail standard IoT Central |
| SMS         | Alerte Azure IoT Central : ${nom_application} - « ${nom_règle} » déclenchée sur « ${nom_appareil} » le ${date_déclenchement} ${date_déclenchement} |
| Voix       | Alerte Azure I.O.T Central : règle « ${nom_règle} » déclenchée sur l’appareil « ${nom_appareil} » le ${date_déclenchement} ${date_déclenchement}, dans l’application ${nom_application} |
| webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[charge utile normale du webhook](howto-create-webhooks.md#payload)}} |

Le texte suivant est un exemple de message SMS provenant d’un groupe d’actions :

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="use-rules-with-iot-edge-modules"></a>Utiliser des règles avec des modules IoT Edge

Une restriction s’applique aux règles qui sont appliquées aux modules IoT Edge. Les règles sur les données de télémétrie provenant de différents modules ne sont pas évaluées en tant que règles valides. Prenons l’exemple suivant : la première condition de la règle concerne une télémétrie de température provenant du module A. La deuxième condition de la règle concerne une télémétrie d’humidité provenant du module B. Comme les deux conditions proviennent de modules différents, votre ensemble de conditions est non valide. La règle n’est pas valide et génère une erreur lorsque vous essayez de l’enregistrer.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer une règle dans votre application Azure IoT Central, vous pouvez apprendre à [Configurer des règles avancées](howto-configure-rules-advanced.md) à l’aide de Power Automate ou d’Azure Logic Apps.
