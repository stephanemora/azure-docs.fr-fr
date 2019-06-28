---
title: Réaction aux événements Azure Media Services | Microsoft Docs
description: Utilisez Azure Event Grid pour vous abonner à des événements Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996122"
---
# <a name="handling-event-grid-events"></a>Traitement des événements Event Grid

Les événements Media Services permettent aux applications de réagir à différents événements (par exemple, l’événement de changement d’état d’un travail) en utilisant des architectures sans serveur modernes. et sans qu’il soit nécessaire de faire appel à du code complexe ou à des services d’interrogation coûteux et inefficaces. Au lieu de cela, les événements sont envoyés via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) aux gestionnaires d’événements, comme [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou même à votre propre Webhook, et vous payez seulement pour ce que vous utilisez. Pour plus d’informations sur la tarification, consultez la page [Tarification Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

La disponibilité des événements Media Services est liée à la [disponibilité](../../event-grid/overview.md) d’Event Grid, et sera proposée dans d’autres régions en même temps qu’Event Grid.  

## <a name="media-services-events-and-schemas"></a>Schémas et événements Media Services

Event Grid utilise les [abonnements aux événements](../../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés. Les événements Media Services contiennent toutes les informations dont vous avez besoin pour répondre aux modifications de vos données. Vous pouvez identifier un événement Media Services, car la propriété eventType commence par « Microsoft.Media ».

Pour plus d’informations, consultez les [schémas d’événement Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Pratiques pour la consommation d’événements

Les applications qui gèrent des événements Media Services doivent suivre certaines pratiques recommandées :

* Comme plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements, il est important de ne pas considérer que les événements proviennent d’une source particulière, mais de vérifier le sujet du message pour vous assurer qu’il provient d’un compte de stockage que vous attendez.
* De même, vérifiez que vous êtes prêt à traiter son eventType, et ne supposez pas que tous les événements reçus seront aux types que vous attendez.
* Ignorez les champs que vous ne comprenez pas.  Cette pratique vous aidera à prendre en charge les nouvelles fonctionnalités qui peuvent être ajoutées à l’avenir.
* Utilisez le préfixe « subject « et les correspondances de suffixe pour limiter les événements à un événement particulier.

## <a name="next-steps"></a>Étapes suivantes

[Obtenir des événements d’état d’un travail](job-state-events-cli-how-to.md)
