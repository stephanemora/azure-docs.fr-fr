---
title: Guide pratique pour spécifier la langue source de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Le SDK Speech vous permet de spécifier la langue source pour la reconnaissance vocale. Cet article explique comment utiliser les méthodes FromConfig et SourceLanguageConfig pour permettre au service Speech de connaître la langue source et de fournir une cible de modèle personnalisé.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: e4f4dd3c1e23855a8a1a69dac72c232779206f1d
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121707"
---
# <a name="specify-source-language-for-speech-to-text"></a>Spécifier la langue source pour la reconnaissance vocale

Dans cet article, vous allez apprendre à spécifier la langue source d’une entrée audio transmise au SDK Speech à des fins de reconnaissance vocale. De plus, un exemple de code est fourni pour spécifier un modèle personnalisé à des fins de reconnaissance améliorée.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Comment spécifier la langue source en C#

Dans cet exemple, la langue source est fournie explicitement sous forme de paramètre utilisant la construction `SpeechRecognizer`.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Dans cet exemple, la langue source est fournie à l’aide de `SourceLanguageConfig`. `sourceLanguageConfig` est ensuite passé sous forme de paramètre à la construction `SpeechRecognizer`.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Dans cet exemple, la langue source et le point de terminaison personnalisé sont fournis à l’aide de `SourceLanguageConfig`. `sourceLanguageConfig` est ensuite passé sous forme de paramètre à la construction `SpeechRecognizer`.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> Les méthodes set `SpeechRecognitionLanguage` et `EndpointId` sont dépréciées dans la classe `SpeechConfig` en C#. L’utilisation de ces méthodes est déconseillée, qui plus est lors de la construction d’un `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Comment spécifier la langue source en C++

Dans cet exemple, la langue source est fournie explicitement sous forme de paramètre à l’aide de la méthode `FromConfig`.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Dans cet exemple, la langue source est fournie à l’aide de `SourceLanguageConfig`. Ensuite, `sourceLanguageConfig` est passé en tant que paramètre à `FromConfig` lors de la création du `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Dans cet exemple, la langue source et le point de terminaison personnalisé sont fournis à l’aide de `SourceLanguageConfig`. `sourceLanguageConfig` est passé en tant que paramètre à `FromConfig` lors de la création du `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` et `SetEndpointId` sont des méthodes dépréciées de la classe `SpeechConfig` en C++ et Java. L’utilisation de ces méthodes est déconseillée, qui plus est lors de la construction d’un `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Comment spécifier la langue source en Java

Dans cet exemple, la langue source est fournie explicitement lors de la création d’un `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Dans cet exemple, la langue source est fournie à l’aide de `SourceLanguageConfig`. Ensuite, `sourceLanguageConfig` est passé en tant que paramètre lors de la création d’un `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Dans cet exemple, la langue source et le point de terminaison personnalisé sont fournis à l’aide de `SourceLanguageConfig`. Ensuite, `sourceLanguageConfig` est passé en tant que paramètre lors de la création d’un `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` et `setEndpointId` sont des méthodes dépréciées de la classe `SpeechConfig` en C++ et Java. L’utilisation de ces méthodes est déconseillée, qui plus est lors de la construction d’un `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Comment spécifier la langue source en Python

La première étape consiste à créer un `speech_config` :

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Ensuite, spécifiez la langue source de l’audio avec `speech_recognition_language` :

```Python
speech_config.speech_recognition_language="de-DE"
```

Si vous utilisez un modèle personnalisé pour la reconnaissance, vous pouvez spécifier le point de terminaison avec `endpoint_id` :

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Comment spécifier la langue source en Javascript

La première étape consiste à créer un `SpeechConfig` :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ensuite, spécifiez la langue source de l’audio avec `speechRecognitionLanguage` :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Si vous utilisez un modèle personnalisé pour la reconnaissance, vous pouvez spécifier le point de terminaison avec `endpointId` :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Comment spécifier la langue source en Objective-C

La première étape consiste à créer un `speechConfig` :

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Ensuite, spécifiez la langue source de l’audio avec `speechRecognitionLanguage` :

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Si vous utilisez un modèle personnalisé pour la reconnaissance, vous pouvez spécifier le point de terminaison avec `endpointId` :

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Voir aussi

* Pour obtenir la liste des langues et paramètres régionaux pris en charge pour la reconnaissance vocale, consultez [Prise en charge des langues](language-support.md).

## <a name="next-steps"></a>Étapes suivantes

* [Documentation de référence du SDK Speech](speech-sdk.md)
