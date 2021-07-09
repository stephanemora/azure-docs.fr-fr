---
title: Guide pratique pour réduire la latence de synthèse vocale avec le SDK Speech
titleSuffix: Azure Cognitive Services
description: Guide pratique pour réduire la latence de synthèse vocale à l’aide du SDK Speech, notamment en utilisant le streaming, la préconnexion, etc.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 8aaa087d1de85b77c9407f8d39e029e3bf2696f2
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614844"
---
# <a name="lower-speech-synthesis-latency-using-speech-sdk"></a>Réduire la latence de synthèse vocale avec le SDK Speech

> [!NOTE]
> Cet article nécessite le SDK Speech 1.17.0 ou une version ultérieure.

La latence de synthèse est essentielle pour vos applications.
Dans cet article, nous allons présenter les bonnes pratiques qui permettent de réduire la latence et de proposer les meilleures performances à vos utilisateurs finaux.

Normalement, nous mesurons la latence par `first byte latency` et `finish latency`, comme suit :

::: zone pivot="programming-language-csharp"

| Latence | Description | Clé de propriété [SpeechSynthesisResult](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) |
|-----------|-------------|------------|
| first byte latency (latence du premier octet) | Indique l’intervalle de temps entre le début de la tâche de synthèse et la réception du premier bloc de données audio. | SpeechServiceResponse_SynthesisFirstByteLatencyMs |
| finish latency (latence de fin) | Indique l’intervalle de temps entre le début de la tâche de synthèse et la réception de l’intégralité des données audio synthétisées. | SpeechServiceResponse_SynthesisFinishLatencyMs |

Le SDK Speech place les durées de latence dans la collection Properties de [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult). L’exemple de code suivant illustre ces valeurs.

```csharp
var result = await synthesizer.SpeakTextAsync(text);
Console.WriteLine($"first byte latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs)} ms");
Console.WriteLine($"finish latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs)} ms");
// you can also get the result id, and send to us when you need help for diagnosis
var resultId = result.ResultId;
```

::: zone-end

::: zone pivot="programming-language-cpp"

| Latence | Description | Clé de propriété [SpeechSynthesisResult](/cpp/cognitive-services/speech/speechsynthesisresult) |
|-----------|-------------|------------|
| `first byte latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception du premier bloc audio. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception de l’intégralité de l’audio synthétisé. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Le SDK Speech a mesuré les latences et les a placées dans le jeu de propriétés de [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult). Reportez-vous aux codes suivants pour les obtenir.

```cpp
auto result = synthesizer->SpeakTextAsync(text).get();
auto firstByteLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFirstByteLatencyMs));
auto finishedLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFinishLatencyMs));
// you can also get the result id, and send to us when you need help for diagnosis
auto resultId = result->ResultId;
```

::: zone-end

::: zone pivot="programming-language-java"

| Latence | Description | Clé de propriété [SpeechSynthesisResult](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) |
|-----------|-------------|------------|
| `first byte latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception du premier bloc audio. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception de l’intégralité de l’audio synthétisé. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Le SDK Speech a mesuré les latences et les a placées dans le jeu de propriétés de [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult). Reportez-vous aux codes suivants pour les obtenir.

```java
SpeechSynthesisResult result = synthesizer.SpeakTextAsync(text).get();
System.out.println("first byte latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs) + " ms.");
System.out.println("finish latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs) + " ms.");
// you can also get the result id, and send to us when you need help for diagnosis
String resultId = result.getResultId();
```

::: zone-end


::: zone pivot="programming-language-python"

| Latence | Description | Clé de propriété [SpeechSynthesisResult](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) |
|-----------|-------------|------------|
| `first byte latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception du premier bloc audio. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception de l’intégralité de l’audio synthétisé. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Le SDK Speech a mesuré les latences et les a placées dans le jeu de propriétés de [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult). Reportez-vous aux codes suivants pour les obtenir.

```python
result = synthesizer.speak_text_async(text).get()
first_byte_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs))
finished_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs))
# you can also get the result id, and send to us when you need help for diagnosis
result_id = result.result_id
```

::: zone-end

::: zone pivot="programming-language-objectivec"

| Latence | Description | Clé de propriété [SPXSpeechSynthesisResult](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) |
|-----------|-------------|------------|
| `first byte latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception du premier bloc audio. | `SPXSpeechServiceResponseSynthesisFirstByteLatencyMs` |
| `finish latency` | Indique l’intervalle de temps entre le début de la synthèse et la réception de l’intégralité de l’audio synthétisé. | `SPXSpeechServiceResponseSynthesisFinishLatencyMs` |

Le SDK Speech a mesuré les latences et les a placées dans le jeu de propriétés de [`SPXSpeechSynthesisResult`](/objectivec/cognitive-services/speech/spxspeechsynthesisresult). Reportez-vous aux codes suivants pour les obtenir.

```Objective-C
SPXSpeechSynthesisResult *speechResult = [speechSynthesizer speakText:text];
int firstByteLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFirstByteLatencyMs]];
int finishedLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFinishLatencyMs]];
// you can also get the result id, and send to us when you need help for diagnosis
NSString *resultId = result.resultId;
```

::: zone-end

Dans la plupart des cas, la latence du premier octet est beaucoup plus faible que la latence de fin.
La latence du premier octet est indépendante de la longueur du texte, tandis que la latence de fin augmente avec la longueur du texte.

Idéalement, nous voulons minimiser la latence expérimentée par l’utilisateur (la latence qui se produit avant que l’utilisateur entende le son) à une seule durée aller-retour réseau additionnée à la latence du premier bloc audio du service de synthèse vocale.

## <a name="streaming"></a>Diffusion en continu

Le streaming est essentiel pour réduire la latence.
Le code client peut commencer la lecture dès que le premier bloc audio est reçu.
Dans un scénario de service, vous pouvez transférer les blocs audio immédiatement à vos clients au lieu d’attendre l’intégralité de l’audio.

::: zone pivot="programming-language-csharp"

Vous pouvez utiliser [`PullAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pullaudiooutputstream), [`PushAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudiooutputstream), l’événement [`Synthesizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing) et [`AudioDateStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) du SDK Speech pour activer le streaming.

Prenons `AudioDateStream` comme exemple :

```csharp
using (var synthesizer = new SpeechSynthesizer(config, null as AudioConfig))
{
    using (var result = await synthesizer.StartSpeakingTextAsync(text))
    {
        using (var audioDataStream = AudioDataStream.FromResult(result))
        {
            byte[] buffer = new byte[16000];
            uint filledSize = 0;
            while ((filledSize = audioDataStream.ReadData(buffer)) > 0)
            {
                Console.WriteLine($"{filledSize} bytes received.");
            }
        }
    }
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

Vous pouvez utiliser [`PullAudioOutputStream`](/cpp/cognitive-services/speech/audio-pullaudiooutputstream), [`PushAudioOutputStream`](/cpp/cognitive-services/speech/audio-pushaudiooutputstream), l’événement [`Synthesizing`](/cpp/cognitive-services/speech/speechsynthesizer#synthesizing) et [`AudioDateStream`](/cpp/cognitive-services/speech/audiodatastream) du SDK Speech pour activer le streaming.

Prenons `AudioDateStream` comme exemple :

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto result = synthesizer->SpeakTextAsync(text).get();
auto audioDataStream = AudioDataStream::FromResult(result);
uint8_t buffer[16000];
uint32_t filledSize = 0;
while ((filledSize = audioDataStream->ReadData(buffer, sizeof(buffer))) > 0)
{
    cout << filledSize << " bytes received." << endl;
}
```

::: zone-end

::: zone pivot="programming-language-java"

Vous pouvez utiliser [`PullAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pullaudiooutputstream), [`PushAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pushaudiooutputstream), l’événement [`Synthesizing`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_Synthesizing) et [`AudioDateStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) du SDK Speech pour activer le streaming.

Prenons `AudioDateStream` comme exemple :

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(config, null);
SpeechSynthesisResult result = synthesizer.StartSpeakingTextAsync(text).get();
AudioDataStream audioDataStream = AudioDataStream.fromResult(result);
byte[] buffer = new byte[16000];
long filledSize = audioDataStream.readData(buffer);
while (filledSize > 0) {
    System.out.println(filledSize + " bytes received.");
    filledSize = audioDataStream.readData(buffer);
}
```

::: zone-end

::: zone pivot="programming-language-python"

Vous pouvez utiliser [`PullAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pullaudiooutputstream), [`PushAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pushaudiooutputstream), l’événement [`Synthesizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#synthesizing) et [`AudioDateStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) du SDK Speech pour activer le streaming.

Prenons `AudioDateStream` comme exemple :

```python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = speech_synthesizer.start_speaking_text_async(text).get()
audio_data_stream = speechsdk.AudioDataStream(result)
audio_buffer = bytes(16000)
filled_size = audio_data_stream.read_data(audio_buffer)
while filled_size > 0:
    print("{} bytes received.".format(filled_size))
    filled_size = audio_data_stream.read_data(audio_buffer)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

Vous pouvez utiliser [`SPXPullAudioOutputStream`](/objectivec/cognitive-services/speech/spxpullaudiooutputstream), [`SPXPushAudioOutputStream`](/objectivec/cognitive-services/speech/spxpushaudiooutputstream), l’événement [`Synthesizing`](/objectivec/cognitive-services/speech/spxspeechsynthesizer#addsynthesizingeventhandler) et [`SPXAudioDataStream`](/objectivec/cognitive-services/speech/spxaudiodatastream) du SDK Speech pour activer le streaming.

Prenons `AudioDateStream` comme exemple :

```Objective-C
SPXSpeechSynthesizer *synthesizer = [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig audioConfiguration:nil];
SPXSpeechSynthesisResult *speechResult = [synthesizer startSpeakingText:inputText];
SPXAudioDataStream *stream = [[SPXAudioDataStream alloc] initFromSynthesisResult:speechResult];
NSMutableData* data = [[NSMutableData alloc]initWithCapacity:16000];
while ([stream readData:data length:16000] > 0) {
    // Read data here
}
```

::: zone-end

## <a name="pre-connect-and-reuse-speechsynthesizer"></a>Préconnecter et réutiliser SpeechSynthesizer

Le SDK Speech utilise un websocket pour communiquer avec le service.
Idéalement, la latence réseau doit correspondre à une seule durée aller-retour (RTT).
Si la connexion vient d’être établie, la latence réseau va inclure du temps supplémentaire pour établir la connexion.
L’établissement d’une connexion websocket nécessite l’établissement d’une liaison TCP, l’établissement d’une liaison SSL, la connexion HTTP et la mise à niveau du protocole, ce qui introduit un délai.
Pour éviter la latence de la connexion, nous vous recommandons de préconnecter et réutiliser `SpeechSynthesizer`.

### <a name="pre-connect"></a>Avant la connexion

Pour la préconnexion, établissez une connexion au service Speech quand vous savez que la connexion sera bientôt nécessaire. Par exemple, si vous générez un bot de message dans le client, vous pouvez vous préconnecter au service de synthèse vocale quand l’utilisateur commence à parler, puis appeler `SpeakTextAsync` quand le texte de réponse du bot est prêt.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(uspConfig, null as AudioConfig))
{
    using (var connection = Connection.FromSpeechSynthesizer(synthesizer))
    {
        connection.Open(true);
    }
    await synthesizer.SpeakTextAsync(text);
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto connection = Connection::FromSpeechSynthesizer(synthesizer);
connection->Open(true);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, (AudioConfig) null);
Connection connection = Connection.fromSpeechSynthesizer(synthesizer);
connection.openConnection(true);
```

::: zone-end

::: zone pivot="programming-language-python"

```python
synthesizer = speechsdk.SpeechSynthesizer(config, None)
connection = speechsdk.Connection.from_speech_synthesizer(synthesizer)
connection.open(True)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer* synthesizer = [[SPXSpeechSynthesizer alloc]initWithSpeechConfiguration:self.speechConfig audioConfiguration:nil];
SPXConnection* connection = [[SPXConnection alloc]initFromSpeechSynthesizer:synthesizer];
[connection open:true];
```

::: zone-end

> [!NOTE]
> Si le texte synthétisé est disponible, appelez simplement `SpeakTextAsync` pour synthétiser l’audio. Le SDK va gérer la connexion.

### <a name="reuse-speechsynthesizer"></a>Réutiliser SpeechSynthesizer

Une autre façon de réduire la latence de la connexion consiste à réutiliser `SpeechSynthesizer` afin de ne pas avoir besoin de créer un `SpeechSynthesizer` pour chaque synthèse.
Nous vous recommandons d’utiliser un pool d’objets dans le scénario de service. Consultez notre exemple de code pour [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) et [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java).


## <a name="transmit-compressed-audio-over-the-network"></a>Transmettre l’audio compressé sur le réseau

Quand le réseau est instable ou sa bande passante limitée, la taille de la charge utile va également avoir un impact sur la latence.
D’un autre côté, un format audio compressé permet d’économiser la bande passante réseau des utilisateurs, ce qui s’avère particulièrement précieux pour les utilisateurs mobiles.

Nous prenons en charge de nombreux formats compressés, notamment `opus`, `webm`, `mp3`, `silk`, etc. Consultez la liste complète dans [SpeechSynthesisOutputFormat](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat).
Par exemple, la vitesse de transmission du format `Riff24Khz16BitMonoPcm` s’élève à 384 Kbits/s, tandis que `Audio24Khz48KBitRateMonoMp3` ne coûte que 48 Kbits/s.
Notre SDK Speech utilise automatiquement un format compressé pour la transmission quand un format de sortie `pcm` est défini et que `GStreamer` est correctement installé.
Reportez-vous à cette [instruction](how-to-use-codec-compressed-audio-input-streams.md) pour installer et configurer `GStreamer` pour le SDK Speech.

## <a name="others-tips"></a>Autres conseils

### <a name="cache-crl-files"></a>Mettre en cache les fichiers de liste de révocation de certificats

Le SDK Speech utilise des fichiers de liste de révocation de certificats pour vérifier la certification.
La mise en cache de ces fichiers jusqu’à leur expiration vous permet d’éviter de les télécharger à chaque fois.
Pour plus d’informations, consultez [Guide pratique pour configurer OpenSSL pour Linux](how-to-configure-openssl-linux.md#certificate-revocation-checks).

### <a name="use-latest-speech-sdk"></a>Utiliser le dernier SDK Speech

Nous améliorons sans cesse les performances du SDK Speech, alors essayez d’utiliser le tout dernier dans votre application.
Par exemple, nous avons résolu un problème de paramètre `TCP_NODELAY` dans la version [1.16.0](releasenotes.md#speech-sdk-1160-2021-march-release), ce qui a permis de réduire la durée d’un aller-retour.

## <a name="load-test-guideline"></a>Instructions de test de charge

Vous pouvez utiliser un test de charge pour tester la capacité et la latence du service de synthèse vocale.
Voici quelques instructions.

 - Le service de synthèse vocale est capable de se mettre à l’échelle automatiquement, mais il a besoin de temps pour effectuer un scale-out. Si la concurrence est augmentée en peu de temps, le client risque d’obtenir une latence longue ou un code d’erreur `429` (trop de demandes). Ainsi, nous vous recommandons d’augmenter votre concurrence progressivement lors du test de charge. [Consultez cet article](speech-services-quotas-and-limits.md#general-best-practices-to-mitigate-throttling-during-autoscaling) pour plus d’informations.
 - Le service a une limite de quota en fonction du trafic réel. Par conséquent, si vous voulez effectuer un test de charge avec une concurrence bien supérieure à votre trafic réel, connectez-vous avant votre test.

## <a name="next-steps"></a>Étapes suivantes

* [Voir les exemples](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master) sur GitHub
