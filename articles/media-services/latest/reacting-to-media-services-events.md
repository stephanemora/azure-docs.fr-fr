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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 3541a5b33aa0bb98d9381b51caefc63b6aa677ad
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377546"
---
# <a name="handling-event-grid-events"></a>Traitement des événements Event Grid

Les événements Media Services permettent aux applications de réagir à différents événements (par exemple, l’événement de changement d’état d’un travail) en utilisant des architectures sans serveur modernes. et sans qu’il soit nécessaire de faire appel à du code complexe ou à des services d’interrogation coûteux et inefficaces. Au lieu de cela, les événements sont envoyés via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) aux gestionnaires d’événements, comme [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou même à votre propre Webhook, et vous payez seulement pour ce que vous utilisez. Pour plus d’informations sur la tarification, consultez la page [Tarification Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

La disponibilité des événements Media Services est liée à la [disponibilité](../../event-grid/overview.md) d’Event Grid, et sera proposée dans d’autres régions en même temps qu’Event Grid.  

## <a name="available-media-services-events"></a>Événements Media Services disponibles

Event Grid utilise les [abonnements aux événements](../../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés.  Actuellement, les abonnements aux événements Media Services peuvent inclure les événements suivants :  

|Nom de l'événement|Description|
|----------|-----------|
| Microsoft.Media.JobStateChange| Déclenché lorsque le travail change d’état. |
| Microsoft.Media.LiveEventConnectionRejected | La tentative de connexion de l’encodeur est rejetée. |
| Microsoft.Media.LiveEventEncoderConnected | L’encodeur établit une connexion avec l’événement en direct. |
| Microsoft.Media.LiveEventEncoderDisconnected | L’encodeur se déconnecte. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Le serveur multimédia supprime le bloc de données, car il est trop tard ou les timestamps se chevauchent (le timestamp du nouveau bloc de données est inférieur à l’heure de fin du bloc de données précédent). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Le serveur multimédia reçoit le premier bloc de données pour chaque piste dans le flux ou la connexion. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Le serveur multimédia détecte des flux audio et vidéo qui ne sont pas synchronisés. Ce type d’événement doit être utilisé comme un avertissement, car l’expérience utilisateur peut ne pas être affectée. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Le serveur multimédia détecte que l’un des deux flux vidéo provenant de l’encodeur externe n’est pas synchronisé. Ce type d’événement doit être utilisé comme un avertissement, car l’expérience utilisateur peut ne pas être affectée. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publié toutes les 20 secondes pour chaque piste pendant l’événement en direct. Fournit un résumé de l’intégrité d’ingestion. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Le serveur multimédia détecte une discontinuité dans la piste entrante. |

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
