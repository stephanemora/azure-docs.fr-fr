---
title: Guide pratique pour utiliser le SDK Speech pour l’évaluation de la prononciation
titleSuffix: Azure Cognitive Services
description: Le SDK Speech prend en charge l’évaluation de la prononciation, qui évalue la qualité de la prononciation des entrées vocales, avec des indicateurs de justesse, d’aisance, d’exhaustivité, et ainsi de suite.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2d1b5e490b7c8212e6103e3d169c1b5491d01dde
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167428"
---
# <a name="pronunciation-assessment"></a>Évaluation de la prononciation

L’évaluation de la prononciation évalue la prononciation des entrées vocales et fournit des commentaires aux orateurs sur la justesse et l’aisance des paroles prononcées.
Grâce à l’évaluation de la prononciation, les élèves qui apprennent des langues peuvent pratiquer, obtenir des commentaires instantanés et améliorer leur prononciation pour pouvoir parler et se présenter en toute confiance.
Les enseignants peuvent utiliser la fonctionnalité pour évaluer la prononciation de plusieurs intervenants en temps réel.

Dans cet article, vous allez découvrir comment configurer `PronunciationAssessmentConfig` et récupérer le `PronunciationAssessmentResult` à l’aide du SDK Speech.

> [!NOTE]
> La fonctionnalité d’évaluation de prononciation prend actuellement en charge la langue `en-US`, qui est disponible dans toutes les [régions de reconnaissance vocale](regions.md#speech-to-text-text-to-speech-and-translation). La prise en charge des langues `en-GB` et `zh-CN` est en préversion, disponible dans les régions `westus`, `eastasia` et `centralindia`.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Évaluation de la prononciation avec le SDK Speech

Dans les exemples ci-dessous, vous allez créer un `PronunciationAssessmentConfig`, puis l’appliquer à un `SpeechRecognizer`.

Les extraits de code suivants montrent comment utiliser la détection automatique de la langue dans vos applications :

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Paramètres de configuration de l’évaluation de la prononciation

Ce tableau liste les paramètres de configuration pour l’évaluation de la prononciation.

| Paramètre | Description | Requis ? |
|-----------|-------------|---------------------|
| ReferenceText | Texte duquel la prononciation est évaluée. | Obligatoire |
| GradingSystem | Système de points pour la calibration du score. Le système `FivePoint` donne un score à virgule flottante de 0 à 5 et `HundredMark` donne un score à virgule flottante de 0 à 100. Par défaut : `FivePoint`. | Facultatif |
| Granularité | Granularité de l’évaluation. Les valeurs acceptées sont `Phoneme`, qui affiche le score au niveau du texte intégral, du mot et du phonème, `Word`, qui affiche le score au niveau du texte intégral et du mot, `FullText`, qui affiche le score au niveau du texte intégral uniquement. Par défaut : `Phoneme`. | Facultatif |
| EnableMiscue | Active le calcul de faute de langue. Quand cette option est activée, les mots prononcés sont comparés au texte de référence et sont marqués comme omission/insertion en fonction de la comparaison. Les valeurs acceptées sont `False` et `True`. Par défaut : `False`. | Facultatif |
| ScenarioId | GUID indiquant un système de points personnalisé. | Facultatif |

### <a name="pronunciation-assessment-result-parameters"></a>Paramètres de résultat de l’évaluation de la prononciation

Ce tableau liste les paramètres de résultat de l’évaluation de la prononciation.

| Paramètre | Description |
|-----------|-------------|
| `AccuracyScore` | Précision de prononciation du discours. La précision indique dans quelle mesure les phonèmes correspondent à la prononciation d’un intervenant de langue maternelle. Le score de précision au niveau du mot et du texte intégral est agrégé à partir du score de précision du niveau du phonème. |
| `FluencyScore` | Fluidité du discours concerné. La fluidité indique dans quelle mesure le discours correspond à l’utilisation qu’un orateur de langue maternelle fait des pauses entre les mots. |
| `CompletenessScore` | Intégralité du discours, déterminée par le calcul du rapport entre les mots prononcés et l’entrée de texte de référence. |
| `PronunciationScore` | Score global indiquant la qualité de prononciation du discours concerné. Il est agrégé à partir de `AccuracyScore`, `FluencyScore` et `CompletenessScore` avec une pondération. |
| `ErrorType` | Cette valeur indique si un mot est omis, inséré ou mal prononcé par rapport à `ReferenceText`. Les valeurs possibles sont `None` (aucune erreur sur ce mot), `Omission`, `Insertion` et `Mispronunciation`. |

### <a name="sample-responses"></a>Exemples de réponses

Résultat typique de l’évaluation de la prononciation en JSON :

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

<!-- TODO: update JavaScript sample links after release -->

* Visionnez l’[introduction vidéo](https://www.youtube.com/watch?v=cBE8CUHOFHQ) et le [tutoriel vidéo](https://www.youtube.com/watch?v=zFlwm7N4Awc) sur l’évaluation de la prononciation

* Testez la [démonstration de l’évaluation de la prononciation](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)

::: zone pivot="programming-language-csharp"
* Consultez l’[exemple de code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) sur GitHub pour l’évaluation de la prononciation.
::: zone-end

::: zone pivot="programming-language-cpp"
* Consultez l’[exemple de code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) sur GitHub pour l’évaluation de la prononciation.
::: zone-end

::: zone pivot="programming-language-java"
* Consultez l’[exemple de code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) sur GitHub pour l’évaluation de la prononciation.
::: zone-end

::: zone pivot="programming-language-python"
* Consultez l’[exemple de code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) sur GitHub pour l’évaluation de la prononciation.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Consultez l’[exemple de code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) sur GitHub pour l’évaluation de la prononciation.
::: zone-end

* [Documentation de référence du SDK Speech](speech-sdk.md)

* [Créez un compte Azure gratuit](https://azure.microsoft.com/free/cognitive-services/)
