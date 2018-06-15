---
title: Réaction aux événements Azure Media Services | Microsoft Docs
description: Utilisez Azure Event Grid pour vous abonner à des événements Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 969957d53824bd70440e5529b83bc830bb5d9cc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782688"
---
# <a name="reacting-to-media-services-events"></a>Réaction aux événements Media Services

Les événements Media Services permettent aux applications de réagir à différents événements (par exemple, l’événement de changement d’état d’un travail) en utilisant des architectures sans serveur modernes. et sans qu’il soit nécessaire de faire appel à du code complexe ou à des services d’interrogation coûteux et inefficaces. Au lieu de cela, les événements sont envoyés via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) aux gestionnaires d’événements, comme [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou même à votre propre Webhook, et vous payez seulement pour ce que vous utilisez. Pour plus d’informations sur la tarification, consultez la page [Tarification Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

La disponibilité des événements Media Services est liée à la [disponibilité](../../event-grid/overview.md) d’Event Grid, et sera proposée dans d’autres régions en même temps qu’Event Grid.  

## <a name="available-media-services-events"></a>Événements Media Services disponibles

Event Grid utilise les [abonnements aux événements](../../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés.  Actuellement, les abonnements aux événements Media Services peuvent inclure le type d’événement suivant :  

|Nom de l'événement|Description|
|----------|-----------|
| Microsoft.Media.JobStateChange| Déclenché lorsque le travail change d’état. |

## <a name="event-schema"></a>Schéma d’événement

Les événements Media Services contiennent toutes les informations dont vous avez besoin pour répondre aux modifications de vos données.  Vous pouvez identifier un événement Media Services, car la propriété eventType commence par « Microsoft.Media ».

Pour plus d’informations, consultez les [schémas d’événement Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Pratiques pour la consommation d’événements

Les applications qui gèrent des événements Media Services doivent suivre certaines pratiques recommandées :

* Comme plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements, il est important de ne pas considérer que les événements proviennent d’une source particulière, mais de vérifier le sujet du message pour vous assurer qu’il provient d’un compte de stockage que vous attendez.
* De même, vérifiez que vous êtes prêt à traiter son eventType, et ne supposez pas que tous les événements reçus seront aux types que vous attendez.
* Ignorez les champs que vous ne comprenez pas.  Cette pratique vous aidera à prendre en charge les nouvelles fonctionnalités qui peuvent être ajoutées à l’avenir.
* Utilisez le préfixe « subject « et les correspondances de suffixe pour limiter les événements à un événement particulier.

## <a name="next-steps"></a>Étapes suivantes

[Obtenir des événements d’état d’un travail](job-state-events-cli-how-to.md)
