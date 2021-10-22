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
ms.openlocfilehash: b71ae256329d9fe8d28f0b5154f0b732de61009f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050543"
---
## <a name="sample-code"></a>Exemple de code
Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/ServerRecording).

## <a name="prerequisites"></a>Prérequis

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 et versions ultérieures)](https://nodejs.org/en/download/)
- [Kit de développement Java(JDK)](/azure/developer/java/fundamentals/java-jdk-install) version 11 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).
- [Infrastructure Spring Boot v 2.5.0](https://spring.io/projects/spring-boot).
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../../create-communication-resource.md). Vous devez noter la **chaîne de connexion** de votre ressource pour ce guide de démarrage rapide
- Un compte de stockage Azure et un conteneur ; pour plus d’informations, consultez [Créer un compte de stockage](../../../../../storage/common/storage-account-create.md?tabs=azure-portal). Vous devez enregistrer votre **chaîne de connexion** et le **nom du conteneur** pour ce démarrage rapide.
- Un webhook [Azure Event Grid](../../../../../event-grid/overview.md).

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des principales fonctionnalités des applications de serveur d’enregistrement de Java.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | Cette classe sert à créer une instance de CallingServerClient.|
| CallingServerClient | Cette classe est nécessaire à la fonctionnalité d’appel. Vous obtenez une instance via CallingServerClientBuilder, et vous l’utilisez pour démarrer/terminer un appel, lire/annuler l’audio et ajouter/supprimer des participants. |

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

Pour créer un client de serveur appelant, vous allez utiliser votre chaîne de connexion Communication Services et la transmettre à l’objet client de serveur appelant.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder builder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
 .connectionString(connectionString);
 callingServerClient = builder.buildClient();
```

## <a name="start-recording-session-using-startrecordingwithresponse-server-api"></a>Démarrer la session d’enregistrement à l’aide de l’API serveur « startRecordingWithResponse »

Utilisez l’ID d’appel du serveur reçu lors du lancement de l’appel.

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");

Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), null, null);
```
La réponse de l’API `startRecordingWithResponse` contient l’ID d’enregistrement de la session d’enregistrement.

## <a name="start-recording-session-with-startrecordingoptions-using-startrecordingwithresponse-server-api"></a>Démarrer la session d’enregistrement avec StartRecordingOptions en utilisant l’API serveur « startRecordingWithResponse »

Utilisez l’ID d’appel du serveur reçu lors du lancement de l’appel.

- RecordingContent permet de passer le type de contenu de l’enregistrement. Exemple : AUDIO/AUDIO_VIDEO.
- RecordingChannel permet de passer le type de canal de l’enregistrement. Exemple : MIXED/UNMIXED.
- RecordingFormat permet de passer le format de l’enregistrement. Exemple : MP4/MP3/WAV.

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");
StartRecordingOptions recordingOptions = new StartRecordingOptions();
recordingOptions.setRecordingContent(RecordingContent.AUDIO_VIDEO);
recordingOptions.setRecordingChannel(RecordingChannel.MIXED);
recordingOptions.setRecordingFormat(RecordingFormat.MP4);
Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), recordingOptions, null);
```

## <a name="stop-recording-session-using-stoprecordingwithresponse-server-api"></a>Arrêter la session d’enregistrement à l’aide de l’API serveur « stopRecordingWithResponse »

Utilisez l’ID d’enregistrement reçu en réponse à `startRecordingWithResponse`.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.stopRecordingWithResponse(recordingId, null);
```

## <a name="pause-recording-session-using-pauserecordingwithresponse-server-api"></a>Suspendre la session d’enregistrement à l’aide de l’API serveur « pauseRecordingWithResponse »

Utilisez l’ID d’enregistrement reçu en réponse à `startRecordingWithResponse`.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.pauseRecordingWithResponse(recordingId, null);
```

## <a name="resume-recording-session-using-resumerecordingwithresponse-server-api"></a>Reprendre la session d’enregistrement à l’aide de l’API serveur « resumeRecordingWithResponse »

Utilisez l’ID d’enregistrement reçu en réponse à `startRecordingWithResponse`.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.resumeRecordingWithResponse(serverCallId, null);
```

## <a name="download-recording-file-using-downloadtowithresponse-server-api"></a>Télécharger le fichier d’enregistrement à l’aide de l’API serveur « downloadToWithResponse »

Utilisez un webhook [Azure Event Grid](../../../../../event-grid/overview.md) ou une autre action déclenchée pour informer vos services lorsque le média enregistré est prêt à être téléchargé.

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
Utilisez la méthode `downloadToWithResponse` de la classe `CallingServerClient` pour télécharger le média enregistré. Les paramètres suivants sont pris en charge pour la méthode `downloadToWithResponse` :

- `contentLocation` : URL d’ACS où se trouve le contenu.
- `destinationPath` : emplacement du fichier.
- `parallelDownloadOptionss` : objet ParallelDownloadOptions facultatif permettant de modifier la façon dont le téléchargement parallèle fonctionne.
- `overwrite` : valeur True pour remplacer le fichier s’il existe.
- `context` : contexte représentant le contexte de la requête.

```Java
Boolean overwrite = true;
ParallelDownloadOptions parallelDownloadOptions = null;
Context context = null;

String filePath = String.format(".\\%s.%s", documentId, fileType);
Path destinationPath = Paths.get(filePath);

Response<Void> downloadResponse = callingServerClient.downloadToWithResponse(contentLocation, destinationPath, parallelDownloadOptions, overwrite, context);
```
L’emplacement du contenu et les ID de document pour les fichiers d’enregistrement peuvent être récupérés respectivement dans les champs `contentLocation` et `documentId` pour chaque `recordingChunk`.
