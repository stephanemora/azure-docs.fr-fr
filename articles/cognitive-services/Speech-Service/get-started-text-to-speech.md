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
ms.date: 09/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: speech-full-stack
keywords: synthèse vocale
ms.openlocfilehash: 79409f95d698e015d15d9131dcf1f27b34b03343
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400900"
---
# <a name="get-started-with-text-to-speech"></a>Bien démarrer avec la conversion de texte par synthèse vocale

Dans ce guide de démarrage rapide, vous découvrez les modèles de conception courants qui permettent d’utiliser la synthèse vocale au moyen du kit SDK Speech. Vous commencez par créer une configuration et une synthèse de base, puis passez à des exemples plus poussés en matière de développement d’applications personnalisées, notamment :

* Obtention de réponses en tant que flux en mémoire
* Personnalisation du taux d’échantillonnage de sortie et de la vitesse de transmission
* Envoi de demandes de synthèse à l’aide de SSML (Speech Synthesis Markup Language)
* Utilisation de voix neurales

> [!TIP]
> Si vous souhaitez passer directement à l’exemple de code, consultez les [exemples de démarrage rapide](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) sur GitHub.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Custom Voice](how-to-custom-voice.md)
* [Améliorer la synthèse avec SSML](speech-synthesis-markup.md)
* Découvrez comment utiliser l’[API Audio long](long-audio-api.md) pour des exemples de textes longs, tels que des livres ou des articles sur l’actualité
* Consultez les [exemples de démarrage rapide](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) sur GitHub
