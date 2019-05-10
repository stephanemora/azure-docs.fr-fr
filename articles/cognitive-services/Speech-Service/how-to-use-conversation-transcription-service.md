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
ms.date: 05/06/2019
ms.author: jhakulin
ms.openlocfilehash: e9de4faf18c54f7c7582ef5a8ab0648629d4f48e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190148"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transcription des conversations plusieurs participantes avec le Speech SDK

Le Kit de développement Speech **ConversationTranscriber** API vous permet de transcrire réunions/conversations avec la possibilité d’ajouter, supprimer et identifier les participants par diffusion en continu d’audio pour les Services de reconnaissance vocale à l’aide de `PullStream` ou `PushStream`.

## <a name="limitations"></a>Limites

* Transcriber de conversation est pris en charge pour C++, C#et Java sur Windows, Linux et Android.
* Le Kit de développement ROOBO est l’environnement matériel pris en charge pour la création de conversations comme qui fournit le tableau microphone multi circulaire qui permettre être utilisée par le service de Transcription de Conversation pour l’identification de l’orateur de façon efficace. [Pour plus d’informations, consultez les appareils de Speech SDK](speech-devices-sdk.md).
* Prise en charge du Kit de développement logiciel de reconnaissance vocale est limité à l’utilisation de l’extraction audio et push des flux en mode avec huit canaux audio PCM.

## <a name="prerequisites"></a>Conditions préalables

* [Découvrez comment utiliser la parole-texte avec le Speech SDK.](quickstart-csharp-dotnet-windows.md)
* [Obtenez votre abonnement d’évaluation de reconnaissance vocale.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>Créer des signatures de voix pour les participants

La première étape consiste à créer des signatures de voix pour les participants de la conversation. Création de signatures de la voix est obligatoire pour l’identification de l’orateur efficace.
Dans l’exemple suivant, nous allons [utiliser l’API REST pour obtenir la signature de la voix.](https://aka.ms/cts/signaturegenservice)

L’exemple ci-dessous montre deux façons de créer des signatures vocal :
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
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
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
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
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

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
