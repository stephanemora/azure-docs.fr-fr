---
title: Transcription de conversation asynchrone (préversion) - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la transcription de conversation asynchrone à l’aide du service Speech. Disponible pour Java uniquement.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: d20cdb2f37c3da357ca112045a0d2845bbb6df98
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260019"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transcription de conversation asynchrone (préversion)

Dans cet article, la transcription de conversation asynchrone est illustrée à l’aide de l’API **RemoteConversationTranscriptionClient**. Si vous avez configuré la transcription de conversation pour effectuer une transcription asynchrone et que vous disposez d’un `conversationId`, vous pouvez associer la transcription à ce `conversationId` à l’aide de l’API **RemoteConversationTranscriptionClient**.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Comparaison entre asynchrone et en temps réel + asynchrone

Avec la transcription asynchrone, vous diffusez l’audio de la conversation, mais une transcription ne doit pas être nécessairement retournée en temps réel. Au lieu de cela, une fois que l’audio est envoyé, utilisez `conversationId` de `Conversation` pour interroger l’état de la transcription asynchrone. Lorsque la transcription asynchrone est prête, vous obtenez un `RemoteConversationTranscriptionResult`.

Avec la transcription en temps réel plus asynchrone, vous obtenez la transcription en temps réel, mais vous pouvez également obtenir la transcription en interrogeant le `conversationId` (similaire au scénario asynchrone).

Deux étapes sont nécessaires pour effectuer une transcription asynchrone. La première étape consiste à charger l’audio, en choisissant asynchrone uniquement ou en temps réel plus asynchrone. La seconde étape consiste à obtenir les résultats de la transcription.

## <a name="upload-the-audio"></a>Charger l’audio

Avant de pouvoir effectuer une transcription asynchrone, vous devez envoyer l’audio au service Transcription de conversation en utilisant le SDK client Microsoft Cognitive Speech (version 1.8.0 ou ultérieure).

Cet exemple de code montre comment créer un transcripteur de conversation pour le mode asynchrone uniquement. Pour diffuser l’audio en continu sur le transcripteur, vous devez ajouter le code de diffusion audio en continu dérivé de [Transcrire des conversations en temps réel avec le Kit de développement logiciel (SDK) Speech](how-to-use-conversation-transcription-service.md). Reportez-vous à la section **Limitations** de cette rubrique pour voir les plateformes et API de langage prises en charge.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Si vous souhaitez une transcription _en temps réel plus asynchrone_, commentez et supprimez les marques de commentaire des lignes de code appropriées comme suit :

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Obtenir les résultats de la transcription

Cette étape permet d’obtenir les résultats de la transcription asynchrone, mais suppose que le traitement en temps réel dont vous pouvez avoir besoin est effectué ailleurs. Pour plus d’informations, consultez [Transcrire des conversations en temps réel avec le Kit de développement logiciel (SDK) Speech](how-to-use-conversation-transcription-service.md).

Pour le code présenté ici, vous avez besoin de **remote-conversation version 1.8.0**, pris en charge uniquement pour Java (1.8.0 ou ultérieur) sur Windows, Linux et Android (niveau d’API 26 ou supérieur uniquement).

### <a name="obtaining-the-client-sdk"></a>Obtention du Kit de développement logiciel (SDK) client

Vous pouvez obtenir **remote-conversation** en modifiant votre fichier pom.xml comme suit.

1. À la fin du fichier, avant la balise de fermeture `</project>`, créez un élément `repositories` avec une référence au référentiel Maven pour le Kit de développement logiciel (SDK) Speech :

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Ajoutez également un élément `dependencies`, avec remoteconversation-client-sdk 1.8.0 en tant que dépendance :

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Enregistrez les modifications

### <a name="sample-transcription-code"></a>Exemple de code de transcription

Une fois que vous disposez du `conversationId`, créez un client de transcription de conversation à distance **RemoteConversationTranscriptionClient** sur l’application cliente pour interroger l’état de la transcription asynchrone. Utilisez la méthode **getTranscriptionOperation** dans **RemoteConversationTranscriptionClient** pour obtenir un objet [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java). L’objet PollerFlux aura des informations sur l’état de l’opération distante **RemoteConversationTranscriptionOperation** et sur le résultat final **RemoteConversationTranscriptionResult**. Une fois l’opération terminée, récupérez **RemoteConversationTranscriptionResult** en appelant **getFinalResult** sur un [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). Dans ce code, nous affichons simplement le contenu du résultat dans la sortie système.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
