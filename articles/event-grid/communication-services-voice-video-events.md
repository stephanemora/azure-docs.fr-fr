---
title: Services de communication Azure - Événements d’appel vocal et vidéo
description: Cet article explique comment utiliser Azure Communication Services comme source d’événement Event Grid pour les événements d’appel vocal et vidéo.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 9259a5ce7151197f1155bfb91f23f3c98afca9b4
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134317"
---
# <a name="azure-communication-services---voice-and-video-calling-events"></a>Services de communication Azure - Événements d’appel vocal et vidéo

Cet article décrit les propriétés et le schéma pour les événements d’appel vocal et vidéo des services de communication.  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

## <a name="events-types"></a>Types d’événements

Les services de communication Azure émettent les types d’événements d’appel vocal et vidéo suivants :

| Type d'événement                                                  | Description                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.RecordingFileStatusUpdated | Publié lorsque le fichier d’enregistrement est disponible |

## <a name="event-responses"></a>Réponses aux événements

Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement aux points de terminaison d’abonnement.

Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement.

> [!IMPORTANT]
> La fonctionnalité d’enregistrement d’appel est toujours en Préversion publique

### <a name="microsoftcommunicationrecordingfilestatusupdated"></a>Microsoft.Communication.RecordingFileStatusUpdated

```json
[
 {
  "id": "7283825e-f8f1-4c61-a9ea-752c56890500",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/recording/call/{call-id}/recordingId/{recording-id}",
  "data": {
    "recordingStorageInfo": {
      "recordingChunks": [
        {
          "documentId": "0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8",
          "index": 0,
          "endReason": "SessionEnded",
          "contentLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/video",
          "metadataLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/acsmetadata"
        }
      ]
    },
    "recordingStartTime": "2021-07-27T15:20:23.6089755Z",
    "recordingDurationMs": 6620,
    "sessionEndReason": "CallEnded"
  },
  "eventType": "Microsoft.Communication.RecordingFileStatusUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-07-27T15:20:34.2199328Z"
 }
]
```
