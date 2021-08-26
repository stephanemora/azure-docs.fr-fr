---
title: Fichier include
description: inclure fichier
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: f025f33a041dd40970b3ea34ff723e1270d16ea6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801495"
---
## <a name="sample-code"></a>Exemple de code
Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ServerRecording).

## <a name="prerequisites"></a>Prérequis

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (12.18.4 et versions ultérieures)](https://nodejs.org/en/download/)
- [Visual Studio (2019 et versions ultérieures)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Veillez à installer la version qui correspond à votre instance de Visual Studio, 32 ou 64 bits.)
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../../create-communication-resource.md). Vous devez noter la **chaîne de connexion** de votre ressource pour ce guide de démarrage rapide
- Un compte Stockage Azure et un conteneur ; pour plus d’informations, consultez [Créer un compte de stockage](../../../../../storage/common/storage-account-create.md?tabs=azure-portal). Vous devez prendre note de votre **chaîne de connexion** de stockage et du **nom du conteneur** pour ce guide de démarrage rapide
- Un Webhook [Azure Event Grid](../../../../../event-grid/overview.md)

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des fonctionnalités principales des applications serveur d’enregistrement de C#.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | Cette classe est nécessaire à la fonctionnalité d’enregistrement. Vous créez une instance de CallingServerClient à l’aide d’une chaîne de connexion de ressource ACS, et vous l’utilisez pour démarrer/arrêter et suspendre/reprendre un enregistrement d’appel. |

## <a name="getting-servercallid-as-a-requirement-for-call-recording-server-apis"></a>Obtention de serverCallId comme condition requise pour les API du serveur d’enregistrement d’appel

> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du Kit de développement logiciel (SDK) web Appel d’ACS. Un exemple de client avec des flux d’enregistrement est disponible sur [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/public-preview).

L’enregistrement des appels est une fonctionnalité étendue de l’API Appel principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité d’enregistrement :

```JavaScript
const callRecordingApi = call.api(Features.Recording);
```

Abonnez-vous aux modifications d’enregistrement :

```JavaScript
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```

Récupérez l’ID d’appel du serveur qui peut être utilisé pour démarrer/arrêter/suspendre/reprendre des sessions d’enregistrement :

Une fois l’appel connecté, utilisez la méthode `getServerCallId` pour obtenir l’ID d’appel du serveur.

```JavaScript
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```

## <a name="create-a-calling-server-client"></a>Créer un client de serveur appelant

Pour créer un client de serveur appelant, vous allez utiliser votre chaîne de connexion Azure Communication Services et la passer à l’objet client de serveur appelant.

```csharp
CallingServerClient callingServerClient = new CallingServerClient("<Resource_Connection_String>");
```

## <a name="start-recording-session-using-startrecordingasync-server-api"></a>Démarrer la session d’enregistrement à l’aide de l’API serveur « StartRecordingAsync »

Utilisez l’ID d’appel du serveur reçu pendant le lancement d’un appel.

```csharp
var startRecordingResponse = await callingServerClient.InitializeServerCall("<servercallid>").StartRecordingAsync("<callbackuri>").ConfigureAwait(false);
```
La réponse de l’API `StartRecordingAsync` contient l’ID d’enregistrement de la session d’enregistrement.

## <a name="stop-recording-session-using-stoprecordingasync-server-api"></a>Arrêter l’enregistrement de la session à l’aide de l’API serveur « StopRecordingAsync »

Utilisez l’ID d’enregistrement reçu en réponse à `StartRecordingAsync`.

```csharp
 var stopRecording = await callingServerClient.InitializeServerCall("<servercallid>").StopRecordingAsync("<recordingid>").ConfigureAwait(false);
```

## <a name="pause-recording-session-using-pauserecordingasync-server-api"></a>Suspendre la session d’enregistrement à l’aide de l’API serveur « PauseRecordingAsync »

Utilisez l’ID d’enregistrement reçu en réponse à `StartRecordingAsync`.

```csharp
var pauseRecording = await callingServerClient.InitializeServerCall("<servercallid>").PauseRecordingAsync("<recordingid>");
```

## <a name="resume-recording-session-using-resumerecordingasync-server-api"></a>Reprendre la session d’enregistrement à l’aide de l’API serveur « ResumeRecordingAsync »

Utilisez l’ID d’enregistrement reçu en réponse à `StartRecordingAsync`.

```csharp
var resumeRecording = await callingServerClient.InitializeServerCall("<servercallid>").ResumeRecordingAsync("<recordingid>");
```

## <a name="download-recording-file-using-downloadstreamingasync-server-api"></a>Télécharger le fichier d’enregistrement à l’aide de l’API serveur « DownloadStreamingAsync »

Utilisez un Webhook [Azure Event Grid](../../../../../event-grid/overview.md) ou une autre action déclenchée pour informer vos services lorsque le média enregistré est prêt à être téléchargé.

Voici un exemple du schéma d’événement.

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "contentLocation": string, //ACS URL where the content is located
                    "metadataLocation": string, // ACS URL where the metadata for this chunk is located
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```

Utilisez l’API `DownloadStreamingAsync` pour télécharger le média enregistré.

```csharp
var recordingDownloadUri = new Uri(downloadLocation);
var response = callingServerClient.DownloadStreamingAsync(recordingDownloadUri);
```
L’emplacement de téléchargement de l’enregistrement peut être récupéré à partir de l’attribut `contentLocation` de `recordingChunk`. La méthode `DownloadStreamingAsync` retourne une réponse de type `Response<Stream>`, qui contient le contenu téléchargé.