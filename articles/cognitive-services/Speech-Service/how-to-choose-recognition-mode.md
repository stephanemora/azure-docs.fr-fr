---
title: Choisir un mode de reconnaissance vocale avec le SDK Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment choisir le meilleur mode de reconnaissance quand vous utilisez le SDK Speech.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d997cb592d9d648998f2b44d9f61f465f05faeb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79079817"
---
# <a name="choose-a-speech-recognition-mode"></a>Choisir un mode de reconnaissance vocale

Si vous envisagez d’effectuer des opérations de reconnaissance vocale, le [SDK Speech](speech-sdk.md) propose plusieurs modes de traitement vocal. D’un point de vue conceptuel, on parle parfois de « *modes de reconnaissance* ». Cet article compare les différents modes de reconnaissance.

## <a name="recognize-once"></a>Reconnaissance ponctuelle

Si vous souhaitez traiter chaque énoncé une « phrase » à la fois, utilisez la fonction de reconnaissance ponctuelle. Cette méthode détecte un énoncé reconnu dans l’entrée allant du début de la parole détectée jusqu’à la pause suivante. En général, une pause marque la fin d’une phrase ou d’une pensée.

À la fin d’un énoncé reconnu, le service cesse de traiter l’audio de cette demande. La limite de reconnaissance maximale est une phrase d’une durée de 20 secondes.

::: zone pivot="programming-language-csharp"

Pour plus d’informations sur l’utilisation de la fonction `RecognizeOnceAsync`, consultez la [documentation sur le SDK Speech pour .NET](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Pour plus d’informations sur l’utilisation de la fonction `RecognizeOnceAsync`, consultez la [documentation sur le SDK Speech pour C++](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Pour plus d’informations sur l’utilisation de la fonction `recognizeOnceAsync`, consultez la [documentation sur le SDK Speech pour Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Pour plus d’informations sur l’utilisation de la fonction `recognize_once`, consultez la [documentation sur le SDK Speech pour Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Pour d’autres langages, consultez la [documentation de référence sur le SDK Speech](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Continue

Si vous avez besoin d’une reconnaissance de longue durée, utilisez une fonction start et une fonction stop correspondante pour mettre en place une reconnaissance continue. La fonction start démarre et continue le traitement de tous les énoncés jusqu’à ce que vous appeliez la fonction stop ou jusqu’à ce qu’une période de silence dépasse une certaine durée. Quand vous utilisez le mode continu, veillez à vous inscrire aux différents événements qui se déclenchent en cas d’occurrence. Par exemple, l’événement « recognized » se déclenche quand la reconnaissance vocale se produit. Vous devez avoir un gestionnaire d’événements en place pour gérer la reconnaissance.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

Pour d’autres langages, consultez la [documentation de référence sur le SDK Speech](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Dictation

Quand vous utilisez la reconnaissance continue, vous pouvez activer le traitement de la dictée en utilisant la fonction « enable dictation » correspondante. Dans ce mode, l’instance de configuration vocale interprète les descriptions verbales de structures de phrase comme la ponctuation. Par exemple, l’énoncé « Habitez-vous en ville point d’interrogation » donne le texte « Habitez-vous en ville ? ».

::: zone pivot="programming-language-csharp"

Pour plus d’informations sur l’utilisation de la fonction `EnableDictation`, consultez la [documentation sur le SDK Speech pour .NET](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Pour plus d’informations sur l’utilisation de la fonction `EnableDictation`, consultez la [documentation sur le SDK Speech pour C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Pour plus d’informations sur l’utilisation de la fonction `enableDictation`, consultez la [documentation sur le SDK Speech pour Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Pour plus d’informations sur l’utilisation de la fonction `enable_dictation`, consultez la [documentation sur le SDK Speech pour Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Pour d’autres langages, consultez la [documentation de référence sur le SDK Speech](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer d’autres exemples du SDK Speech sur GitHub](https://aka.ms/csspeech/samples)
