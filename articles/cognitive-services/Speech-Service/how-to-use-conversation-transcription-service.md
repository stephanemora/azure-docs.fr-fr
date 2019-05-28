---
title: Plusieurs participantes conversations avec le Speech SDK - Services Speech de transcrire
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la Transcription de Conversation avec le Speech SDK. Disponible pour C++, C#et Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: 80ec606fee30c239d47bca94188d3b9cbb7c82d5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604414"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transcription des conversations plusieurs participantes avec le Speech SDK

Le Kit de développement Speech **ConversationTranscriber** API vous permet de transcrire réunions/conversations avec la possibilité d’ajouter, supprimer et identifier les participants par diffusion en continu d’audio pour les Services de reconnaissance vocale à l’aide de `PullStream` ou `PushStream`.

## <a name="limitations"></a>Limites

* Transcriber de conversation est pris en charge pour C++, C#et Java sur Windows, Linux et Android.
* Le Kit de développement ROOBO est l’environnement matériel pris en charge pour la création des transcriptions de conversation qui fournit efficacement tableau microphone multi circulaire qui peut être utilisé pour l’identification de l’orateur. [Pour plus d’informations, consultez les appareils de Speech SDK](speech-devices-sdk.md).
* Prise en charge SDK de reconnaissance vocale pour la transcription de conversation est limité à l’utilisation de l’extraction audio et push des flux en mode avec huit canaux audio PCM 16 kHz 16 bits.
* Transcription de conversation est actuellement disponible dans les langues « en-US » et « zh-CN » dans les régions suivantes : centralus et eastasia.

## <a name="prerequisites"></a>Conditions préalables

* [Découvrez comment utiliser la parole-texte avec le Speech SDK.](quickstart-csharp-dotnet-windows.md)
* [Obtenez votre abonnement d’évaluation de reconnaissance vocale.](https://azure.microsoft.com/try/cognitive-services/)
* Speech SDK version 1.5.1 ou ultérieure est requise.

## <a name="create-voice-signatures-for-participants"></a>Créer des signatures de voix pour les participants

La première étape consiste à créer des signatures de voix pour les participants de la conversation. Création de signatures de la voix est obligatoire pour l’identification de l’orateur efficace.

### <a name="requirements-for-input-wave-file"></a>Configuration requise pour le fichier d’entrée audio

* Le fichier d’entrée wave audio pour la création de signatures de la voix doit être dans les exemples de 16 bits, taux d’échantillonnage kHz 16 et un format de canal unique (Mono).
* La longueur minimale recommandée pour chaque échantillon audio est comprise entre 30 secondes et de deux minutes.

L’exemple suivant montre deux façons de créer la signature vocale par [à l’aide de l’API REST]. (https://aka.ms/cts/signaturegenservice) de C#:

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

Pour la transcription des conversations avec plusieurs participants, créer le `ConversationTranscriber` objet auquel est associé le `AudioConfig` objet créé pour la session de conversation et le flux audio à l’aide `PullAudioInputStream` ou `PushAudioInputStream`.

Supposons que vous disposez d’une classe ConversationTranscriber appelée `MyConversationTranscriber`. Votre code peut ressembler à ceci :

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
