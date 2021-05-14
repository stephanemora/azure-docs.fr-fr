---
title: Vue d’ensemble de l’enregistrement d’appel Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Fournit une vue d’ensemble de la fonctionnalité d’enregistrement d’appel et des API associées.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730269"
---
# <a name="calling-recording-overview"></a>Vue d’ensemble de l’enregistrement d’appel

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> De nombreux pays et états ont des lois et des réglementations qui s’appliquent à l’enregistrement des appels RTC, vocaux et vidéo ; elles exigent souvent le consentement des utilisateurs pour procéder à l’enregistrement de leurs communications. Il vous incombe d’utiliser les fonctionnalités d’enregistrement d’appel en conformité avec la loi. Vous devez obtenir le consentement des parties impliquées dans les communications enregistrées, d’une manière qui soit conforme aux lois applicables à chaque participant.

> [!NOTE]
> Les réglementations relatives à la maintenance des données personnelles exigent la possibilité d’exporter des données utilisateur. Afin de prendre en charge ces exigences, l’enregistrement des fichiers de métadonnées inclut l’ID participant de chaque participant à un appel dans le tableau `participants`. Vous pouvez croiser les MRI dans le tableau `participants` avec vos identités d’utilisateur internes pour identifier les participants dans un appel. Un exemple de fichier de métadonnées d’enregistrement est fourni ci-dessous pour référence.

L’enregistrement d’appel fournit un ensemble d’API permettant de démarrer, d’arrêter, de suspendre et de reprendre l’enregistrement. Ces API sont accessibles à partir de la logique métier côté serveur, ou via des événements déclenchés par des actions utilisateur. La sortie multimédia enregistrée est au format `MP4 Audio+Video`, qui est le même format que celui utilisé par Teams pour enregistrer des données multimédias. Les notifications liées au contenu multimédia et aux métadonnées sont émises via Event Grid. Les enregistrements sont stockés pendant 48 heures sur un stockage temporaire intégré, en prévision de leur récupération et de leur déplacement vers une solution de stockage à long terme de votre choix. 

## <a name="run-time-control-apis"></a>API de contrôle à l’exécution
Les API de contrôle à l’exécution peuvent être utilisées pour gérer l’enregistrement via des déclencheurs de logique métier internes, tels qu’une application créant un appel de groupe et enregistrant la conversation, ou à partir d’une action déclenchée par l’utilisateur qui indique à l’application serveur de démarrer l’enregistrement. Dans les deux cas, `<conversation-id>` est nécessaire pour enregistrer une réunion ou un appel spécifique. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Obtention de l’ID de conversation à partir d’un appel initié par un serveur

Un `ConversationId` est retourné par le biais de l’événement `Microsoft.Communication.CallLegStateChanged`. Cette notification d’événement est émise après l’établissement d’un appel. Il figure dans le champ `data.ConversationId`. Cette valeur peut être utilisée directement comme paramètre `{conversationId}` dans les API de contrôle à l’exécution :
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>Obtention de l’ID de conversation d’un événement déclenché par l’utilisateur sur le client

À partir de la bibliothèque `@azure/communication-calling` JavaScript, après avoir établi un appel invoke `let result = call.info.getConversationUrl()` pour obtenir l’élément `conversationUrl`, **Base64Url code `conversationUrl` pour obtenir `{conversationId}` et l’utiliser dans les API de contrôle à l’exécution**. L’encodage peut être effectué sur le client avant d’envoyer l’événement au serveur, ou côté serveur.

Notez que `conversationUrl` *doit* être encodé en Base64Url, à ne pas confondre avec l’encodage Base64 simple (c.-à-d. btoa).                                                            

### <a name="start-recording"></a>Démarrer l’enregistrement

#### <a name="request"></a>Requête

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**Kit de développement logiciel (SDK) C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>response

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Obtenir l’état de l’enregistrement d’appel

#### <a name="request"></a>Requête

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**Kit de développement logiciel (SDK) C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>response

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Arrêter l’enregistrement
#### <a name="request"></a>Requête
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**Kit de développement logiciel (SDK) C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Suspendre l'enregistrement
La suspension et la reprise de l’enregistrement d’appel vous permettent d’ignorer l’enregistrement d’une partie d’un appel ou d’une réunion, et de reprendre l’enregistrement dans un fichier unique. 
#### <a name="request"></a>Requête
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**Kit de développement logiciel (SDK) C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Reprendre l’enregistrement
#### <a name="request"></a>Requête
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**Kit de développement logiciel (SDK) C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Types de sorties multimédias
L’enregistrement d’appel prend actuellement en charge le format de sortie MP4 audio mixte+vidéo. Les données multimédias de sortie correspondent aux enregistrements de réunion produits par le biais de l’enregistrement Microsoft Teams.

| Type de canal | Format du contenu | Vidéo | Audio |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | vidéo 1920x1080 8 FPS de tous les participants dans la disposition des vignettes par défaut | audio mixte mp4a 16kHz de tous les participants |

## <a name="event-grid-notifications"></a>Notifications Event Grid
Une notification Event Grid `Microsoft.Communication.RecordingFileStatusUpdated` est publiée lorsqu’un enregistrement est prêt pour la récupération, en général 1 à 2 minutes après la fin du processus d’enregistrement (par exemple, réunion terminée, enregistrement arrêté). Les notifications d’événements d’enregistrement incluent un ID de document, qui peut être utilisé pour récupérer les informations multimédias enregistrées et un fichier de métadonnées d’enregistrement :

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Vous trouverez des exemples de code pour gérer les notifications Event Grid et télécharger des fichiers d’enregistrement et de métadonnées [ici](../../quickstarts/voice-video-calling/download-recording-file-sample.md). 

### <a name="notification-schema"></a>Schéma de notification
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Téléchargement de fichier

> Azure Communication Services fournit un stockage multimédia à court terme pour les enregistrements. **Exportez le contenu enregistré que vous souhaitez conserver sous 48 heures.** Après 48 heures, les enregistrements ne seront plus disponibles.

### <a name="download-recording"></a>Télécharger l’enregistrement
#### <a name="request"></a>Requête
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Télécharger les métadonnées d’enregistrement
#### <a name="request"></a>Requête
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
