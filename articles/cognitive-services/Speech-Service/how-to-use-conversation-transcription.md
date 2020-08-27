---
title: Transcription de conversation en temps réel (préversion) - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la transcription de conversation en temps réel avec le SDK Speech. Disponible pour C++, C# et Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.custom: devx-track-csharp
ms.openlocfilehash: a425c75dfd57f0d5f9c98b73d8b351972b70703a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918653"
---
# <a name="real-time-conversation-transcription-preview"></a>Transcription de conversation en temps réel (préversion)

L’API **ConversationTranscriber** du SDK Speech vous permet de transcrire des réunions et d’autres conversations avec la possibilité d’ajouter, supprimer et identifier des participants en diffusant en streaming du contenu audio vers le service Speech à l’aide de `PullStream` ou `PushStream`. Cette rubrique exige de savoir comment utiliser la Reconnaissance vocale avec le kit de développement logiciel (SDK) Speech (version 1.8.0 ou ultérieure). Pour plus d’informations, consultez [Qu’est-ce que le service Speech ?](overview.md).

## <a name="limitations"></a>Limites

- L’API ConversationTranscriber est prise en charge pour C++, C# et Java sur Windows, Linux et Android.
- Actuellement disponible dans les langues « en-US » et « zh-CN » dans les régions suivantes : _centralus_ et _eastasia_.
- Nécessite un réseau circulaire de sept microphones avec un flux de référence de lecture. Le réseau de microphones doit respecter [nos spécifications](https://aka.ms/sdsdk-microphone).
- Le [SDK Speech Devices](speech-devices-sdk.md) fournit des appareils appropriés et un exemple d’application illustrant la transcription de conversation.

## <a name="optional-sample-code-resources"></a>Exemples de ressources de code facultatifs

Le SDK Speech Devices fournit un exemple de code Java pour la capture audio en temps réel sur huit canaux.

- [Exemple de code d’appareil ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Exemple de code du kit de développement Azure Kinect](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Prérequis

Un abonnement au service Speech. Vous pouvez créer un [compte gratuit Azure](https://azure.microsoft.com/free/cognitive-services/) si vous n’en avez pas.

## <a name="create-voice-signatures"></a>Créer des signatures vocales

La première étape consiste à créer des signatures vocales pour les orateurs à la conversation afin d’obtenir une identification de l’orateur efficace.

### <a name="audio-input-requirements"></a>Exigences d’entrée audio

- Le fichier son d’entrée permettant de créer des signatures vocales doit correspondre à un échantillon de 16 bits, à un taux d’échantillonnage de 16 KHz et à un format à canal unique (mono).
- La longueur recommandée de chaque échantillon audio se situe entre trente secondes et deux minutes.

### <a name="sample-code"></a>Exemple de code

L’exemple suivant montre deux façons de créer une signature vocale à l’aide de l’[API REST](https://aka.ms/cts/signaturegenservice) en C#. Notez que vous devrez remplacer « YourSubscriptionKey » par les informations réelles, « speakerVoice. wav » par le nom de votre fichier wave, ainsi que `{region}` et « YourServiceRegion » par votre région (_central_ ou _eastasia_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Transcrire des conversations

L’exemple de code suivant montre comment transcrire les conversations en temps réel pour trois intervenants. Il part du principe que vous avez déjà créé des signatures vocales pour chaque intervenant, comme indiqué ci-dessus. Substituez les informations réelles pour « YourSubscriptionKey » et « YourServiceRegion » lors de la création de l’objet SpeechConfig.

Voici quelques exemples de code :

- Création d’un objet `Conversation` à partir de l’objet `SpeechConfig` à l’aide d’un identificateur de réunion généré à l’aide de `Guid.NewGuid()`
- Création d’un objet `ConversationTranscriber` et participation à la conversation avec `JoinConversationAsync()` pour démarrer la transcription
- Enregistrement des événements intéressants
- Ajout ou suppression de participants à la conversation à l’aide de l’objet Conversation
- Diffusion audio en continu
- Dans le kit de développement logiciel (SDK) Speech version 1.9.0 (et versions ultérieures), les types de valeur `int` et `string` sont tous deux pris en charge dans le champ de la version de la signature vocale.

La transcription et l’identificateur de l’intervenant reviennent dans les événements enregistrés.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber(audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transcription de conversation asynchrone](how-to-async-conversation-transcription.md)
