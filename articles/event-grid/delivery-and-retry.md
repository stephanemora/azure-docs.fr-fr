---
title: Distribution et nouvelle tentative de distribution avec Azure Event Grid
description: Décrit comment Azure Event Grid distribue des événements et gère les messages qui n’ont pas été distribués.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: b34386a7b416d6f7d8b008a9cb5ef142948a370f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005393"
---
# <a name="event-grid-message-delivery-and-retry"></a>Distribution et nouvelle tentative de distribution de messages avec Azure Grid 

Cet article décrit comment Azure Event Grid gère les événements en l’absence d’accusé de réception d’une distribution.

Event Grid assure une distribution fiable. Il distribue chaque message au moins une fois pour chaque abonnement. Les événements sont envoyés immédiatement au webhook inscrit de chaque abonnement. Si un webhook n’accuse pas réception d’un événement dans les 60 secondes suivant la première tentative de distribution, Event Grid effectue une nouvelle tentative de distribution de l’événement. 

Actuellement, Event Grid envoie chaque événement individuellement aux abonnés. L’abonné reçoit un tableau ne comprenant qu’un seul événement.

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
- 414 URI trop long
- 429 Trop de demandes
- 500 Erreur interne du serveur
- 503 Service indisponible
- 504 Dépassement du délai de la passerelle

Si Event Grid reçoit une erreur qui indique que le point de terminaison n’est pas disponible temporairement ou qu’une prochaine requête peut aboutir, il tente à nouveau d’envoyer l’événement. Si Event Grid reçoit une erreur qui indique que la remise ne réussira jamais et qu’un [point de terminaison de lettres mortes a été configuré](manage-event-delivery.md), il envoie l’événement au point de terminaison de lettres mortes. 

## <a name="retry-intervals-and-duration"></a>Intervalles avant nouvelle tentative et durée

Event Grid utilise une stratégie de nouvelle tentative d’interruption exponentielle pour la distribution des événements. Si votre webhook ne répond pas ou s’il retourne un code d’échec, Event Grid effectue une nouvelle tentative de distribution aux intervalles suivants :

1. 10 secondes
2. 30 secondes
3. 1 minute
4. 5 minutes
5. 10 minutes
6. 30 minutes
7. 1 heure

Event Grid ajoute une petite randomisation à tous les intervalles de nouvelle tentative. La remise des événements est renouvelée après une heure, une fois par heure.

Par défaut, Event Grid fait expirer tous les événements qui ne sont pas distribués dans les 24 heures. Vous pouvez [personnaliser la stratégie de nouvelle tentative](manage-event-delivery.md) lors de la création d’un abonnement à un événement. Vous fournissez le nombre maximal de tentatives de remise (par défaut, 30) et la durée de vie de l’événement (par défaut, 1 440 minutes).

## <a name="dead-letter-events"></a>Événements de lettres mortes

Quand Event Grid ne parvient pas à remettre un événement, il peut envoyer cet événement non remis à un compte de stockage. Ce processus est appelé mise en file d’attente de lettres mortes. Pour voir les événements non remis, vous pouvez les extraire de l’emplacement des lettres mortes. Pour plus d’informations, voir [Stratégies de lettres mortes et de nouvelles tentatives](manage-event-delivery.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher l’état des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour personnaliser les options de remise d’événement, voir [Gérer les paramètres de remise d’Event Grid](manage-event-delivery.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).