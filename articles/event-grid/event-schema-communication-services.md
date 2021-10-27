---
title: Azure Communication Services comme source Event Grid - Vue d’ensemble
description: Cet article explique comment utiliser Azure Communication Services comme source d’événement Event Grid.
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: mikben
ms.openlocfilehash: 590e0a53ca701a6b293dd52b2f7c30c5e873df30
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129488"
---
# <a name="event-handling-in-azure-communication-services"></a>Gestion des événements dans Azure Communication Services

Azure Communication Services s’intègre à [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pour fournir des notifications d’événements en temps réel de manière fiable, scalable et sécurisée. L’objectif de cet article est de vous aider à configurer vos applications pour être à l’écoute des événements Communication Services. Par exemple, vous pouvez mettre à jour une base de données, créer un élément de travail et envoyer une notification Push chaque fois qu’un SMS est reçu par un numéro de téléphone associé à votre ressource Communication Services.

Azure Event Grid est un service de routage d’événement complètement managé qui utilise le modèle publication-abonnement. Event Grid offre une prise en charge intégrée des services Azure comme [Azure Functions](../azure-functions/functions-overview.md) et [Azure Logic Apps](../azure-functions/functions-overview.md). Il peut envoyer des alertes d’événement à des services non-Azure à l’aide de webhooks. Pour obtenir une liste complète des gestionnaires d’événements qui prennent en charge Event Grid, consultez [Présentation d’Azure Event Grid](overview.md).

:::image type="content" source="./media/overview/functional-model.png" alt-text="Schéma montrant le modèle d’événement d’Azure Event Grid.":::

> [!NOTE]
> Pour en savoir plus sur le rapport entre la résidence des données et la gestion des événements, consultez la [documentation conceptuelle relative à la résidence des données](../communication-services/concepts/privacy.md).

## <a name="events-types"></a>Types d’événements

Event Grid utilise les [abonnements aux événements](concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés.

Azure Communication Services émet les types d’événements suivants :

* [Événements de conversation](./communication-services-chat-events.md)
* [Événements téléphoniques et SMS](./communication-services-telephony-sms-events.md)
* [Événements d’appel vocal et vidéo](./communication-services-voice-video-events.md)
* [Événements de présence](./communication-services-presence-events.md)

Vous pouvez utiliser le portail Azure ou Azure CLI pour vous abonner aux événements émis par votre ressource Communication Services. 

## <a name="event-subjects"></a>Objets des événements

Le champ `subject` de tous les événements Communication Services identifie l’utilisateur, le numéro de téléphone ou l’entité que l’événement cible. Les préfixes courants sont utilisés pour autoriser un [filtrage Event Grid](event-filtering.md) simple.

| Préfixe de l’objet                              | Entité du service de communication |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | Numéro de téléphone RTPC            |
| `user/`                                     | Utilisateur de Communication Services  |
| `thread/`                                   | Fil de conversation.                 |

L’exemple suivant montre un filtre pour tous les SMS et tous les rapports de remise envoyés à tous les numéros de téléphone dotés de l’indicatif régional 555 détenus par une ressource Communication Services :

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](./overview.md).
* Pour obtenir une présentation des concepts d’Azure Event Grid, consultez [Concepts utilisés dans Azure Event Grid](./concepts.md).
* Pour obtenir une présentation des rubriques système d’Azure Event Grid, consultez [Rubriques système dans Azure Event Grid](./system-topics.md).
