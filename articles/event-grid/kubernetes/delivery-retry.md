---
title: Azure Event Grid sur Kubernetes - Distribution des événements et nouvelles tentatives
description: Cet article explique comment Event Grid sur Kubernetes avec Azure Arc distribue les événements et comment il gère les messages non distribués.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: c62ffc4374a461036d2e766fd57d96ec1906face
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385655"
---
# <a name="event-grid-on-kubernetes---event-delivery-and-retry"></a>Event Grid sur Kubernetes - Distribution des événements et nouvelles tentatives
Event Grid sur Kubernetes avec Azure Arc tente de distribuer immédiatement chaque message au moins une fois pour chaque abonnement correspondant. S’il n’obtient pas une réponse HTTP 200 de réussite de la part de l’abonné ou s’il y a un échec, Event Grid Kubernetes retente la distribution en fonction d’une planification fixe des nouvelles tentatives et d’une stratégie de nouvelle tentative. 

Par défaut, Event Grid sur Kubernetes distribue un seul événement à la fois à l’abonné. Cependant, la charge utile de la demande de distribution est un tableau avec un seul événement. Il peut distribuer plusieurs événements à la fois si vous activez la fonctionnalité de traitement par lot des sorties. Pour plus d’informations sur cette fonctionnalité, consultez [Distribution des événements par lot](batch-event-delivery.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]

> [!NOTE]
> Pendant la préversion, les fonctionnalités d’Event Grid sur Kubernetes sont prises en charge via l’API version [2020-10-15-Preview](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate). 


## <a name="retry-schedule"></a>Planification des nouvelles tentatives
Event Grid sur Kubernetes attend une réponse pendant jusqu’à 60 secondes après la distribution d’un événement. Si le point de terminaison de l’abonné n’envoie pas de réponse de réussite (HTTP 200 ou un équivalent), il retente de distribuer l’événement. Voici comment cela fonctionne. 

1. Le message arrive dans Event Grid pour Kubernetes. Une tentative de livraison immédiate est effectuée.
1. Si la distribution échoue, le message est placé dans une file d’attente de 1 minute, à l’issue de laquelle sa distribution est retentée.
1. Si la distribution échoue à nouveau, le message est placé dans une file d’attente de 10 minutes, à l’issue desquelles sa distribution est retentée toutes les 10 minutes.
1. Les livraisons sont tentées jusqu’à ce que la livraison réussisse ou que les limites de stratégie de nouvelles tentatives soient atteintes.
 
## <a name="retry-policy"></a>Stratégie de nouvelle tentative
Deux configurations déterminent la stratégie de nouvelles tentatives. Les voici :

- Nombre maximal de tentatives
- Durée de vie (TTL) de l’événement

Un événement est supprimé si une des limites de la stratégie de nouvelle tentative est atteinte. La configuration de ces limites est effectuée par abonnement. La section suivante décrit chacune d’elles de façon plus détaillées.

### <a name="configuring-defaults-per-subscriber"></a>Configuration des valeurs par défaut par abonné
Vous pouvez également spécifier des limites de stratégie de nouvelles tentatives pour chaque abonnement. Pour plus d’informations sur la configuration des valeurs par défaut par abonné, consultez notre [documentation sur l’API](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate). Les valeurs par défaut du niveau d’abonnement remplacent les configurations de niveau du module Event Grid sur Kubernetes.

L’exemple suivant configure un abonnement de Webhook avec `maxNumberOfAttempts` défini sur 3 et `eventTimeToLiveInMinutes` défini sur 30 minutes.

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": " CloudEventSchemaV1_0"
   }
  },
  "retryPolicy": {
   "eventTimeToLiveInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les destinations et les gestionnaires pris en charge par Event Grid sur Azure Arc pour Kubernetes, consultez [Event Grid sur Kubernetes - Gestionnaires d’événements](event-handlers.md).