---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: pafarley
ms.openlocfilehash: 0ffa432b6d5d94ad4e313a183d8e277ce44aae3d
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129584958"
---
## <a name="install-the-speech-sdk"></a>Installer le Kit de développement logiciel (SDK) Speech

Avant de pouvoir faire quoi que ce soit, vous devez installer le SDK Speech. Suivez les instructions ci-dessous, en fonction de votre plateforme :

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-voice-signatures"></a>Créer des signatures vocales

(Vous pouvez ignorer cette étape si vous ne souhaitez pas utiliser les profils utilisateur pré-inscrits pour identifier des participants spécifiques.)

Si vous souhaitez inscrire des profils utilisateur, la première étape consiste à créer des signatures vocales pour les participants à la conversation afin qu’ils puissent être identifiés en tant qu’orateurs uniques. Le fichier son `.wav` d’entrée permettant de créer des signatures vocales doit correspondre à 16 bits, à un taux d’échantillonnage de 16 kHz et à un format à canal unique (mono). La longueur recommandée de chaque échantillon audio se situe entre trente secondes et deux minutes. Un échantillon audio trop court entraînera une réduction de la précision lors de la reconnaissance de l’orateur. Le fichier `.wav` doit être un échantillon de voix **d’une personne** afin qu’un profil vocal unique puisse être créé.

L’exemple suivant montre comment créer une signature vocale à l’aide de l’[API REST](https://aka.ms/cts/signaturegenservice) en C#. Notez que vous devez remplacer `subscriptionKey`, `region` et le chemin d’un fichier d’échantillon `.wav` par des valeurs réelles.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Runtime.Serialization;
using System.Threading.Tasks;
using Newtonsoft.Json;

[DataContract]
internal class VoiceSignature
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public VoiceSignatureData Signature { get; private set; }

    [DataMember]
    public string Transcription { get; private set; }
}

[DataContract]
internal class VoiceSignatureData
{
    internal VoiceSignatureData()
    { }

    internal VoiceSignatureData(int version, string tag, string data)
    {
        this.Version = version;
        this.Tag = tag;
        this.Data = data;
    }

    [DataMember]
    public int Version { get; private set; }

    [DataMember]
    public string Tag { get; private set; }

    [DataMember]
    public string Data { get; private set; }
}

private static async Task<string> GetVoiceSignatureString()
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";

    byte[] fileBytes = File.ReadAllBytes("path-to-voice-sample.wav");
    var content = new ByteArrayContent(fileBytes);
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
    
    var jsonData = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<VoiceSignature>(jsonData);
    return JsonConvert.SerializeObject(result.Signature);
}
```

L’exécution de la fonction `GetVoiceSignatureString()` retourne une chaîne de signature vocale dans le format approprié. Exécutez la fonction à deux reprises afin d’utiliser deux chaînes comme entrée pour les variables `voiceSignatureStringUser1` et `voiceSignatureStringUser2` ci-dessous.

> [!NOTE]
> Les signatures vocales peuvent **uniquement** être créées à l’aide de l’API REST.

## <a name="transcribe-conversations"></a>Transcrire des conversations

L’exemple de code suivant montre comment transcrire les conversations en temps réel pour deux intervenants. Il part du principe que vous avez déjà créé des chaînes de signature vocale pour chaque intervenant, comme indiqué ci-dessus. Remplacez `subscriptionKey`, `region` et le chemin `filepath` pour le son que vous souhaitez transcrire par des informations réelles.

Si vous n’utilisez pas de profils utilisateur préinscrits, quelques secondes supplémentaires seront nécessaires pour effectuer la première reconnaissance des utilisateurs inconnus en tant que Speaker1, speaker2, etc.

> [!NOTE]
> Assurez-vous que le même `subscriptionKey` est utilisé dans votre application pour la création de la signature, autrement vous rencontrerez des erreurs. 

Cet exemple de code effectue les opérations suivantes :

* Crée un `AudioConfig` à partir de l’exemple du fichier d’échantillon `.wav` à transcrire.
* Crée une `Conversation` à l’aide de `CreateConversationAsync()`.
* Crée un `ConversationTranscriber` à l’aide du constructeur et s’abonne aux événements nécessaires.
* Ajoute des participants à la conversation. Les chaînes `voiceSignatureStringUser1` et `voiceSignatureStringUser2` doivent être générées en sortie des étapes ci-dessus à partir de la fonction `GetVoiceSignatureString()`.
* Joint la conversation et commence la transcription.

> [!NOTE]
> `AudioStreamReader` est une classe d’assistance que vous pouvez obtenir sur [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/csharp/dotnet/conversation-transcription/helloworld/AudioStreamReader.cs).

Appelez la fonction `TranscribeConversationsAsync()` pour démarrer la transcription de la conversation.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

class transcribe_conversation
{
// all your other code

public static async Task TranscribeConversationsAsync(string voiceSignatureStringUser1, string voiceSignatureStringUser2)
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav";

    var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
    // config.SpeechRecognitionLanguage = "zh-cn"; // en-us by default. This code specifies Chinese.
    var stopRecognition = new TaskCompletionSource<int>();

    using (var audioInput = AudioConfig.FromWavFileInput(filepath))
    {
        var meetingID = Guid.NewGuid().ToString();
        using (var conversation = await Conversation.CreateConversationAsync(config, meetingID))
        {
            // create a conversation transcriber using audio stream input
            using (var conversationTranscriber = new ConversationTranscriber(audioInput))
            {
                conversationTranscriber.Transcribing += (s, e) =>
                {
                    Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                };

                conversationTranscriber.Transcribed += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
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
                        stopRecognition.TrySetResult(0);
                    }
                };

                conversationTranscriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine($"\nSession started event. SessionId={e.SessionId}");
                };

                conversationTranscriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine($"\nSession stopped event. SessionId={e.SessionId}");
                    Console.WriteLine("\nStop recognition.");
                    stopRecognition.TrySetResult(0);
                };

                // Add participants to the conversation.
                var speaker1 = Participant.From("User1", "en-US", voiceSignatureStringUser1);
                var speaker2 = Participant.From("User2", "en-US", voiceSignatureStringUser2);
                await conversation.AddParticipantAsync(speaker1);
                await conversation.AddParticipantAsync(speaker2);

                // Join to the conversation and start transcribing
                await conversationTranscriber.JoinConversationAsync(conversation);
                await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                // waits for completion, then stop transcription
                Task.WaitAny(new[] { stopRecognition.Task });
                await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
            }
         }
      }
   }
}
```

