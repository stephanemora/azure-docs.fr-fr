---
title: Diffuser en streaming du contenu audio compressé par codec avec le SDK Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment diffuser en streaming du contenu audio compressé sur le service Speech avec le SDK Speech. Disponible pour C++, C#et Java pour Linux, Java dans Android et Objective-C dans iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 410c0942b9040a6707a51e4ff9f375b9d4728668
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821568"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Utiliser une entrée audio compressée par codec avec le SDK Speech

L’API **Compressed Audio Input Stream** du SDK du service Speech permet de diffuser en streaming du contenu audio compressé vers le service Speech à l’aide d’un `PullStream` ou d’un `PushStream`.

Plateforme | Languages | Version de GStreamer prise en charge
| :--- | ---: | :---:
Windows (sauf UWP)  | C++, C#, Java, Python | [1.15.1](https://gstreamer.freedesktop.org/releases/gstreamer/1.5.1.html)
Linux  | C++, C#, Java, Python | [architectures cibles et distributions Linux prises en charge](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Version du SDK Speech requise pour l’entrée audio compressée
* Le Kit de développement logiciel (SDK) Speech version 1.10.0 ou ultérieure est requis pour RHEL 8 et CentOS 8.
* Le Kit de développement logiciel (SDK) Speech version 1.11.0 ou ultérieure est requis pour Windows.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer requis pour gérer l’audio compressé

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemple de code utilisant une entrée audio compressé par codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir comment effectuer la reconnaissance vocale](./get-started-speech-to-text.md)