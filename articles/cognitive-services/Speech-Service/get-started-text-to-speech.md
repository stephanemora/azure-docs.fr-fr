---
title: Guide de démarrage rapide sur la synthèse vocale - Service de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser le kit SDK Speech pour convertir le texte par synthèse vocale. Dans ce guide de démarrage rapide, vous allez découvrir la construction d'objets et les modèles de conception, les formats de sortie audio pris en charge, le CLI Speech, ainsi que les options de configuration personnalisées pour la synthèse vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/17/2021
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: synthèse vocale
ms.openlocfilehash: 33e19816d219e2be3dae367c5b30da349e2eb879
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110085483"
---
# <a name="get-started-with-text-to-speech"></a>Bien démarrer avec la conversion de texte par synthèse vocale

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Obtenir des informations sur la position

Votre projet peut avoir besoin de connaître le moment où un mot est prononcé par la conversion de texte par synthèse vocale, afin de pouvoir effectuer une action spécifique en fonction de ce timing.
Par exemple, si vous souhaitez mettre en évidence des mots à mesure qu’il sont prononcés, vous devez savoir ce qu’il faut mettre en évidence, à quel moment et pendant combien de temps.

Pour cela, vous pouvez utiliser l’événement `WordBoundary` disponible dans `SpeechSynthesizer`.
Cet événement est déclenché au début de chaque nouveau mot prononcé, et fournit un décalage temporel dans le flux parlé ainsi qu’un décalage de texte dans l’invite d’entrée.

* `AudioOffset` signale le temps écoulé dans l’audio de sortie entre le début de la synthèse et le début du mot suivant. Cette valeur est mesurée en centaines de nanosecondes (HNS), 10 000 HNS équivalant à 1 milliseconde.
* `WordOffset` signale la position du caractère dans la chaîne d’entrée (texte d’origine ou [SSML](speech-synthesis-markup.md)) juste avant le mot sur le point d’être prononcé.

> [!NOTE]
> Les événements `WordBoundary` sont déclenchés à mesure que les données audio de sortie deviennent disponibles (ce qui va plus vite que la lecture sur un appareil de sortie). Il appartient à l’appelant de synchroniser de manière appropriée le timing du flux avec le « temps réel ».

Vous trouverez des exemples d’utilisation de `WordBoundary` dans les [exemples de synthèse vocale](https://aka.ms/csspeech/samples) sur GitHub.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Custom Voice](how-to-custom-voice.md)
* [Améliorer la synthèse avec SSML](speech-synthesis-markup.md)
* Découvrez comment utiliser l’[API Audio long](long-audio-api.md) pour des exemples de textes longs, tels que des livres ou des articles sur l’actualité
* Consultez les [exemples de démarrage rapide](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) sur GitHub
