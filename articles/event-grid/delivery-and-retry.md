---
title: Distribution et nouvelle tentative de distribution avec Azure Event Grid
description: Décrit comment Azure Event Grid distribue des événements et gère les messages qui n’ont pas été distribués.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: b4bfdd3e9cdf99314dc55907ba163adc6cd39423
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65952889"
---
# <a name="event-grid-message-delivery-and-retry"></a>Distribution et nouvelle tentative de distribution de messages avec Azure Grid

Cet article décrit comment Azure Event Grid gère les événements en l’absence d’accusé de réception d’une distribution.

Event Grid assure une distribution fiable. Il distribue chaque message au moins une fois pour chaque abonnement. Les événements sont envoyés immédiatement au point de terminaison inscrit de chaque abonnement. Si un point de terminaison n’accuse pas réception d’un événement, Event Grid effectue une nouvelle tentative de distribution.

Actuellement, Event Grid envoie chaque événement individuellement aux abonnés. L’abonné reçoit un tableau ne comprenant qu’un seul événement.

## <a name="retry-schedule-and-duration"></a>Planification d’un nouvel essai et durée

Event Grid attend une réponse pendant 30 secondes après la distribution d’un message. Après 30 secondes, si le point de terminaison n’a pas répondu, le message est mis en file d’attente en vue d’une nouvelle tentative. Event Grid utilise une stratégie de nouvelle tentative d’interruption exponentielle pour la distribution des événements. Dans la mesure du possible, Event Grid tente une nouvelle livraison selon la planification suivante :

- 10 secondes
- 30 secondes
- 1 minute
- 5 minutes
- 10 minutes
- 30 minutes
- 1 heure
- Toutes les heures jusqu’à 24 heures

Si le point de terminaison répond dans les 3 minutes, Event Grid tente de supprimer l’événement de la file d’attente de nouvelle tentative dans la mesure du possible, mais des doublons peuvent toujours être reçus.

Event Grid ajoute une légère randomisation à toutes les étapes de nouvelle tentative et peut ignorer de façon opportuniste certaines nouvelles tentatives si un point de terminaison est systématiquement non sain, inactif pendant une longue période ou semble être surchargé.

Pour un comportement déterministe, définissez la durée de l’événement de durée de vie et de nombre maximum de tentatives de remise dans les [stratégies de nouvelle tentative d’abonnement](manage-event-delivery.md).

Par défaut, Event Grid fait expirer tous les événements qui ne sont pas distribués dans les 24 heures. Vous pouvez [personnaliser la stratégie de nouvelle tentative](manage-event-delivery.md) lors de la création d’un abonnement à un événement. Vous fournissez le nombre maximal de tentatives de remise (par défaut, 30) et la durée de vie de l’événement (par défaut, 1 440 minutes).

## <a name="dead-letter-events"></a>Événements de lettres mortes

Quand Event Grid ne parvient pas à remettre un événement, il peut envoyer cet événement non remis à un compte de stockage. Ce processus est appelé mise en file d’attente de lettres mortes. Par défaut, Event Grid n’active pas cette fonctionnalité. Pour l’activer, vous devez spécifier le compte de stockage dans lequel les événements non remis seront conservés au moment de créer l’abonnement aux événements. Les événements sont extraits de ce compte de stockage pour résoudre les remises.

Event Grid envoie un événement à l’emplacement des lettres mortes lorsqu’il a effectué toutes ses nouvelles tentatives. Si Event Grid reçoit un code de réponse 400 (requête incorrecte) ou 413 (entité de requête trop grande), il envoie immédiatement l’événement au point de terminaison des lettres mortes. Ces codes de réponse indiquent que la diffusion de l’événement va échouer.

Un délai de cinq minutes s’écoule entre la dernière tentative de remise d’un événement et le moment de sa remise à l’emplacement des lettres mortes. Ce décalage est destiné à réduire le nombre d’opérations de stockage d’objets blob. Si l’emplacement des lettres mortes est indisponible pendant quatre heures, l’événement est abandonné.

Avant de définir l’emplacement des lettres mortes, vous devez disposer d’un compte de stockage avec un conteneur. Vous devez indiquer le point de terminaison de ce conteneur au moment de créer l’abonnement aux événements. Le point de terminaison se présente sous la forme suivante : `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Vous souhaiterez peut-être être averti lorsqu’un événement a été envoyé à l’emplacement des lettres mortes. Pour répondre aux événements non remis à l’aide d’Event Grid, [créez un abonnement aux événements](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pour le stockage Blob de lettres mortes. Chaque fois que votre stockage Blob de lettres mortes reçoit un événement non remis, Event Grid notifie votre gestionnaire. Le gestionnaire répond par les mesures que vous voulez prendre pour réconcilier les événements non remis.

Pour savoir comment configurer un emplacement de lettres mortes, voir [Stratégies de lettres mortes et de nouvelles tentatives](manage-event-delivery.md).

## <a name="message-delivery-status"></a>État de distribution du message

Event Grid utilise les codes de réponse HTTP pour accuser réception des événements. 

### <a name="success-codes"></a>Codes de réussite

Les codes de réponse HTTP suivants indiquent qu’un événement a bien été distribué à votre webhook. Event Grid considère que la distribution est effectuée.

- 200 OK
- 202 Accepté

### <a name="failure-codes"></a>Codes d’échec

Les codes de réponse HTTP suivants indiquent un échec de la tentative de distribution d’un événement.

- 400 Demande incorrecte
- 401 Non autorisé
- 404 Introuvable
- 408 Délai d’expiration de la demande
- 413 Entité de demande trop grande
- 414 URI trop long
- 429 Trop de demandes
- 500 Erreur interne du serveur
- 503 Service indisponible
- 504 Dépassement du délai de la passerelle

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher l’état des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour personnaliser les options de diffusion d’événements, consultez [Stratégies de lettres mortes et de nouvelles tentatives](manage-event-delivery.md).