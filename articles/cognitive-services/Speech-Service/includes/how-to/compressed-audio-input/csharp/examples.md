---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 9739256cbe3381224913048904763a6e28da2eab
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109805373"
---
Pour configurer le SDK Speech afin d’accepter l’entrée audio compressée, créez `PullAudioInputStream` ou `PushAudioInputStream`. Ensuite, créez un `AudioConfig` à partir d’une instance de votre classe de flux en spécifiant le format de compression du flux. Recherchez des exemples d’extraits de code connexes dans [À propos de l’API de flux d’entrée audio du Kit de développement logiciel (SDK) Speech](/azure/cognitive-services/speech-service/how-to-use-audio-input-streams).

Supposons que vous disposez d’une classe de flux d’entrée appelée `pushStream` et que vous utilisez OPUS/OGG. Voici à quoi peut ressembler votre code :

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pushStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```
