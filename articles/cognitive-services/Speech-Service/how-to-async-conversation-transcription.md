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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506492"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transcription de conversation asynchrone (préversion)

Dans cet article, la transcription de conversation asynchrone est illustrée à l’aide de l’API **RemoteConversationTranscriptionClient**. Si vous avez configuré la transcription de conversation pour effectuer une transcription asynchrone et que vous disposez d’un `conversationId`, vous pouvez associer la transcription à ce `conversationId` à l’aide de l’API **RemoteConversationTranscriptionClient**.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Comparaison entre asynchrone et en temps réel + asynchrone

Avec la transcription asynchrone, vous diffusez l’audio de la conversation, mais une transcription ne doit pas être nécessairement retournée en temps réel. Au lieu de cela, une fois que l’audio est envoyé, utilisez `conversationId` de `ConversationTranscriber` pour interroger l’état de la transcription asynchrone. Lorsque la transcription asynchrone est prête, vous obtenez un `RemoteConversationTranscriptionResult`.

Avec la transcription en temps réel plus asynchrone, vous obtenez la transcription en temps réel, mais vous pouvez également obtenir la transcription en interrogeant le `conversationId` (similaire au scénario asynchrone).

Deux étapes sont nécessaires pour effectuer une transcription asynchrone. La première étape consiste à charger l’audio, en choisissant asynchrone uniquement ou en temps réel plus asynchrone. La seconde étape consiste à obtenir les résultats de la transcription.

## <a name="upload-the-audio"></a>Charger l’audio

Avant de pouvoir effectuer une transcription asynchrone, vous devez envoyer l’audio à Transcription de conversation à l’aide du Kit de développement logiciel (SDK) client Microsoft Cognitive Speech (version 1.8.0 ou ultérieure).

Cet exemple de code montre comment créer un transcripteur de conversation pour le mode asynchrone uniquement. Pour diffuser l’audio en continu sur le transcripteur, vous devez ajouter le code de diffusion audio en continu dérivé de [Transcrire des conversations en temps réel avec le Kit de développement logiciel (SDK) Speech](how-to-use-conversation-transcription-service.md). Reportez-vous à la section **Limitations** de cette rubrique pour voir les plateformes et API de langage prises en charge.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
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

Pour le code présenté ici, vous avez besoin de **remoteconversation-client-sdk version 1.0.0**, pris en charge uniquement pour Java (1.8 ou version ultérieure) sur Windows, Linux et Android (niveau d’API 26 ou supérieur).

### <a name="obtaining-the-client-sdk"></a>Obtention du Kit de développement logiciel (SDK) client

Vous pouvez obtenir **remoteconversation-client-sdk** en modifiant votre fichier pom.xml comme suit.

- À la fin du fichier, avant la balise de fermeture `</project>`, créez un élément `repositories` avec une référence au référentiel Maven pour le Kit de développement logiciel (SDK) Speech :

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Ajoutez également un élément `dependencies`, avec remoteconversation-client-sdk 1.0.0 en tant que dépendance :

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- Enregistrez les modifications

### <a name="sample-transcription-code"></a>Exemple de code de transcription

Une fois que vous disposez du `conversationId`, créez un objet d’opération à distance **RemoteConversationTranscriptionOperation** sur le client pour interroger l’état de la transcription asynchrone. **RemoteConversationTranscriptionOperation** est étendu à partir de l’[interrogateur](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). Une fois l’interrogateur terminé, récupérez **RemoteConversationTranscriptionResult** en vous abonnant à l’interrogateur et en interrogeant l’objet. Dans ce code, nous affichons simplement le contenu du résultat dans la sortie système.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
