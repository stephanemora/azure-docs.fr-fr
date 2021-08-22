---
title: Azure Event Grid sur Kubernetes – Schémas d’événements
description: Cet article décrit les schémas d’événements que prend en charge Event Grid sur Azure Arc pour Kubernetes
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 4ec482a0cf8c0b418d2cd6ec11d1afd56273b681
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415621"
---
# <a name="event-schemas-in-event-grid-on-kubernetes"></a>Schémas d’événements dans Event Grid sur Kubernetes
Event Grid sur Kubernetes accepte et livre des événements au format JSON. Il prend en charge la [spécification de schéma CloudEvents 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md), et il s’agit du schéma à utiliser lors de la publication d’événements dans Event Grid. 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



## <a name="cloudevent-schema"></a>Schéma CloudEvents
[CloudEvents](https://cloudevents.io/) est une spécification ouverte qui décrit les données d’événement. Elle simplifie l’interopérabilité en proposant un schéma d’événement commun pour la publication et la consommation des événements. Pour plus d’informations sur les attributs de contexte obligatoires, consultez [Spécification CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope).

## <a name="example--event-using-cloudevents-schema"></a>Exemple : événement utilisant le schéma CloudEvents

```json
[{
      "specversion": "1.0",
      "type" : "orderCreated",
      "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
      "id" : "eventId-n",
      "time" : "2020-12-25T20:54:07+00:00",
      "subject" : "account/acct-123224/order/o-123456",
      "dataSchema" : "1.0",
      "data" : {
         "orderId" : "123",
         "orderType" : "PO",
         "reference" : "https://www.myCompanyName.com/orders/123"
      }
}]
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les destinations et les gestionnaires pris en charge par Event Grid sur Azure Arc pour Kubernetes, consultez [Event Grid sur Kubernetes - Gestionnaires d’événements](event-handlers.md).