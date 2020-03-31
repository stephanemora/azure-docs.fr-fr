---
title: Schéma des événements Azure SignalR dans Azure Event Grid
description: Décrit les propriétés qui sont fournies pour les événements Azure SignalR avec Azure Event Grid
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67788512"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Schéma des événements Azure Event Grid pour SignalR Service

Cet article décrit les propriétés et le schéma des événements SignalR Service. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).


## <a name="available-event-types"></a>Types d’événement disponibles

SignalR Service émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Déclenché lorsqu’une connexion client est établie. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Déclenché lorsqu’une connexion client est interrompue. |

## <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre le schéma d’un événement de connexion client : 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Le schéma pour un événement de déconnexion de client est similaire : 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| topic | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | string | Identificateur unique de l’événement. |
| data | object | Données d’événement SignalR Service. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| timestamp | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| hubName | string | Le hub auquel appartient la connexion cliente. |
| connectionId | string | Identificateur unique de la connexion client. |
| userId | string | L’identificateur d’utilisateur défini dans la revendication. |
| errorMessage | string | L’erreur qui provoque la déconnexion. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
