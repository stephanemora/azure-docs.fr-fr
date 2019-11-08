---
title: 'Procédure : Utiliser la détection automatique de la langue pour la reconnaissance vocale – Service de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Le Kit de développement logiciel (SDK) Speech prend en charge la détection automatique de la langue pour la reconnaissance vocale. Quand vous utilisez cette fonctionnalité, l’audio fourni est comparé à une liste de langues fournie, et la correspondance la plus probable est déterminée. Vous pouvez utiliser la valeur retournée pour sélectionner le modèle de langage utilisé pour la reconnaissance vocale.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: e156704d3ad54c3437f921fae536a497e1d94868
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506488"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Détection automatique de la langue pour la reconnaissance vocale

La détection automatique de la langue est utilisée pour déterminer la correspondance la plus probable au son transmis au Kit de développement logiciel (SDK) Speech lorsqu’il est comparé à une liste de langues fournies. La valeur retournée par la détection automatique de la langue est ensuite utilisée pour sélectionner le modèle de langue pour la reconnaissance vocale, ce qui vous donne une transcription plus précise. Pour connaître les langues disponibles, voir [Prise en charge linguistique](language-support.md).

Dans cet article, vous allez apprendre à utiliser `AutoDetectSourceLanguageConfig` pour construire un objet `SpeechRecognizer` et récupérer la langue détectée.

> [!IMPORTANT]
> Cette fonctionnalité est disponible uniquement pour le Kit de développement logiciel (SDK) Speech pour C++ et pour Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Détection automatique de la langue avec le Kit de développement logiciel (SDK) Speech

La détection automatique de la langue a actuellement une limite côté services de deux langues par détection. Gardez cette limitation à l’esprit lors de la construction de votre objet `AudoDetectSourceLanguageConfig`. Dans les exemples ci-dessous, vous allez créer un `AutoDetectSourceLanguageConfig`, puis l’utiliser pour construire une `SpeechRecognizer`.

>[!TIP]
> Vous pouvez également spécifier un modèle personnalisé à utiliser lors de l’exécution de la reconnaissance vocale. Pour plus d’informations, voir [Utiliser un modèle personnalisé pour la détection automatique de la langue](#use-a-custom-model-for-automatic-language-detection).

Les extraits de code suivants montrent comment utiliser la détection automatique de la langue dans vos applications :

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Utiliser un modèle personnalisé pour la détection automatique de la langue

En plus de la détection de la langue à l’aide de modèles de service Speech, vous pouvez spécifier un modèle personnalisé pour une reconnaissance améliorée. À défaut de modèle personnalisé, le service utilise le modèle de langue par défaut.

Les extraits de code ci-dessous montrent comment spécifier un modèle personnalisé dans votre appel au service Speech. Si la langue détectée est `en-US`, le modèle par défaut est utilisé. Si la langue détectée est `fr-FR`, le point de terminaison du modèle personnalisé est utilisé :

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Étapes suivantes

* [Documentation de référence du SDK Speech](speech-sdk.md)
