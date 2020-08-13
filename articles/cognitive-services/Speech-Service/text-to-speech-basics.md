---
title: Bases de la synthèse vocale - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser le kit SDK Speech pour convertir le texte par synthèse vocale. Dans cet article, vous allez découvrir la construction d’objets, les formats d’entrée audio pris en charge et les options de configuration personnalisée pour la synthèse vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: c5ceb76b8dcdad5d487e196cf1780703b7e34f17
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874518"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Découvrir les principes de base de la synthèse vocale

Dans cet article, vous découvrez les modèles de conception courants qui permettent d’utiliser la synthèse vocale au moyen du kit SDK Speech. Vous commencez par créer une configuration et une synthèse de base, puis passez à des exemples plus poussés en matière de développement d’applications personnalisées, notamment :

* Obtention de réponses en tant que flux en mémoire
* Personnalisation du taux d’échantillonnage de sortie et de la vitesse de transmission
* Envoi de demandes de synthèse à l’aide de SSML (Speech Synthesis Markup Language)
* Utilisation de voix neurales

> [!TIP]
> Si vous n’avez pas encore eu l’occasion de suivre l’un de nos guides de démarrage rapide, nous vous encourageons à essayer la synthèse vocale par vous-même.
> * [Synthétiser la parole vers un haut-parleur](quickstarts/text-to-speech.md)

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

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Custom Voice](how-to-custom-voice.md)
* [Améliorer la synthèse avec SSML](speech-synthesis-markup.md)
