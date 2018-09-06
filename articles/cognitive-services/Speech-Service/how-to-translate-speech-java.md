---
title: Traduire une entrée vocale à l’aide du kit de développement logiciel (SDK) Speech pour Java
titleSuffix: Microsoft Cognitive Services
description: Montre comment traduire une entrée vocale à l’aide du kit de développement logiciel (SDK) Speech pour Java.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: cddea05a237936fd9a2cc6a4d807c6b1f300077c
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144612"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-java"></a>Traduire une entrée vocale avec le kit de développement logiciel Cognitive Services Speech pour Java

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-java [Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-java [Translation from file input](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez le code utilisé dans cet article au sein du dossier samples/java/jre/console.

## <a name="next-steps"></a>Étapes suivantes

- [Comment effectuer une reconnaissance vocale](how-to-recognize-speech-java.md)
- [Comment reconnaître les intentions vocales](how-to-recognize-intents-from-speech-java.md)
