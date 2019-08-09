---
title: Transcrire des conversations à plusieurs participants avec le SDK Speech – Speech Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la transcription de conversation avec le SDK Speech. Disponible pour C++, C# et Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: f0838d345abfcfdf69ca6ea44d3206c23010b457
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073083"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transcrire des conversations à plusieurs participants avec le SDK Speech

L’API **ConversationTranscriber** du SDK Speech vous permet de transcrire des réunions/conversations avec la possibilité d’ajouter, supprimer et identifier des participants en diffusant en streaming du contenu audio vers Speech Services à l’aide de `PullStream` ou `PushStream`.

## <a name="limitations"></a>Limites

* L’API de transcription de conversation est prise en charge pour C++, C# et Java sur Windows, Linux et Android.
* Le kit de développement ROOBO est l’environnement matériel pris en charge pour la création de transcriptions de conversation, car il offre un ensemble circulaire de micros qui permet d’identifier efficacement l’orateur. [Pour plus d’informations, consultez Kit de développement logiciel (SDK) de dispositifs vocaux](speech-devices-sdk.md).
* La prise en charge du SDK Speech pour la transcription de conversation est limitée à l’utilisation de flux en mode pull et push avec huit canaux audio PCM de 16 kHz et 16 bits.
* La transcription de conversation est actuellement disponible dans les langues « en-US » et « zh-CN » dans les régions suivantes : centralus et eastasia.

## <a name="prerequisites"></a>Prérequis

* [Découvrez comment utiliser la reconnaissance vocale avec le SDK Speech.](quickstart-csharp-dotnet-windows.md)
* [Obtenez votre abonnement d’essai gratuit de Speech.](https://azure.microsoft.com/try/cognitive-services/)
* Le SDK Speech version 1.5.1 ou ultérieure est requis.

## <a name="create-voice-signatures-for-participants"></a>Créer des signatures vocales pour les participants

La première étape consiste à créer des signatures vocales pour les participants à la conversation. Pour une identification efficace des orateurs, il est nécessaire de créer des signatures vocales.

### <a name="requirements-for-input-wave-file"></a>Configuration requise du fichier son d’entrée

* Le fichier son d’entrée permettant de créer des signatures vocales doit correspondre à un échantillon de 16 bits, à un taux d’échantillonnage de 16 KHz et à un format à canal unique (mono).
* La longueur recommandée pour chaque échantillon audio se situe entre 30 secondes et deux minutes.

L’exemple suivant montre deux façons de créer une signature vocale à l’aide de l’[API REST](https://aka.ms/cts/signaturegenservice) en C# :

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Transcription de conversations

Pour transcrire des conversations à plusieurs participants, créez l’objet `ConversationTranscriber` associé à l’objet `AudioConfig` créé pour la session de conversation et diffusez en streaming le flux audio à l’aide de `PullAudioInputStream` ou `PushAudioInputStream`.

Supposons que vous disposez d’une classe ConversationTranscriber appelée `MyConversationTranscriber`. Votre code peut se présenter comme suit :

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
