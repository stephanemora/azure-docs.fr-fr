---
title: Azure Communication Services – Événements de présence
description: Cet article explique comment utiliser Azure Communication Services comme source d’événement Event Grid pour les événements de présence utilisateur.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 75e23d5de55ab205cc35bf6e7496413f4dd4be9a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134323"
---
# <a name="azure-communication-services---presence-events"></a>Azure Communication Services – Événements de présence

Cet article décrit les propriétés et le schéma des événements de présence de Communication Services.  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

## <a name="events-types"></a>Types d’événements

Azure Communication Services émet les types d’événements de présence utilisateur suivants :

| Type d'événement                                                  | Description                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.UserDisconnected | Publié après qu’un utilisateur de Communication Services est désigné comme s’étant déconnecté de Communication Services |

## <a name="event-responses"></a>Réponses aux événements

Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement aux points de terminaison d’abonnement.

Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement.

> [!IMPORTANT]
> L’événement Microsoft.Communication.UserDisconnected est en préversion publique. Pendant cette période de préversion, les journaux associés à l’état déconnecté de l’utilisateur peuvent être répliqués globalement. Vous pouvez obtenir l’état déconnecté en vous abonnant à cet événement via Event Grid.

> [!NOTE]
> L’événement Microsoft.Communication.UserDisconnected s’applique uniquement dans le contexte d’une conversation.
 
### <a name="microsoftcommunicationuserdisconnected"></a>Microsoft.Communication.UserDisconnected

```json
[
 {
  "id": "8f60490d-0719-4d9d-a1a6-835362fb752e",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "user/{rawId}",
  "data": {
    "userCommunicationIdentifier": {
      "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40",
      "communicationUser": {
        "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40"
      }
    }
  },
  "eventType": "Microsoft.Communication.UserDisconnected",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-08-10T20:25:38Z"
 }
]
```
