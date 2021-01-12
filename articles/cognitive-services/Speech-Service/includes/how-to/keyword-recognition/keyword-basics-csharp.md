---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 786f9587ab223cf87a48cd791f366049b94af59b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97865974"
---
Tout d’abord, chargez votre fichier de modèle de mot clé à l’aide de la fonction statique `FromFile()`, qui retourne un élément `KeywordRecognitionModel`. Utilisez le chemin du fichier `.table` que vous avez téléchargé à partir de Speech Studio. De plus, vous créez un fichier `AudioConfig` à l’aide du microphone par défaut, puis instanciez un nouveau `KeywordRecognizer` à l’aide de la configuration audio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Ensuite, l’exécution de la reconnaissance de mot clé s’effectue avec un appel à `RecognizeOnceAsync()` en transmettant votre objet de modèle. Cela démarre une session de reconnaissance de mots clés qui dure jusqu’à ce que le mot clé soit reconnu. Ainsi, vous utilisez généralement ce modèle de conception dans les applications multithread ou dans les cas d’utilisation où vous pouvez attendre indéfiniment un mot de mise en éveil.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> L’exemple indiqué ici utilise la reconnaissance de mot clé locale, car il ne demande pas d’objet `SpeechConfig` pour le contexte d’authentification et ne contacte pas le back-end. Toutefois, vous pouvez exécuter la reconnaissance de mot clé et la vérification [en utilisant une connexion directe au back-end](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Reconnaissance continue

D’autres classes du Kit de développement logiciel (SDK) Speech prennent en charge la reconnaissance continue (pour la reconnaissance vocale et intentionnelle) avec reconnaissance de mot clé. Cela vous permet d’utiliser le même code que celui que vous utiliseriez normalement pour la reconnaissance continue, avec la possibilité de référencer un fichier `.table` pour votre modèle de mot clé.

Pour la reconnaissance vocale, suivez le même modèle de conception que celui indiqué dans le [démarrage rapide](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-to-text?tabs=script%2Cbrowser%2Cwindowsinstall&pivots=programming-language-csharp#continuous-recognition) pour configurer la reconnaissance continue. Ensuite, remplacez l’appel à `recognizer.StartContinuousRecognitionAsync()` par `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` et transmettez votre objet `KeywordRecognitionModel`. Pour arrêter la reconnaissance continue avec détection de mots clés, utilisez `recognizer.StopKeywordRecognitionAsync()` au lieu de `recognizer.StopContinuousRecognitionAsync()`.

La reconnaissance intentionnelle utilise un modèle identique avec les fonctions [`StartKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) et [`StopKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync).