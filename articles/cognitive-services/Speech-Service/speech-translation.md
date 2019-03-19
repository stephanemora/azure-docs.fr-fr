---
title: Traduction vocale avec Speech Azure Services
titlesuffix: Azure Cognitive Services
description: Les Services de reconnaissance vocale vous permettent d’ajouter une traduction de bout en bout, en temps réel, multilingue de reconnaissance vocale à vos applications, les outils et les appareils. La même API peut être utilisée pour la traduction de parole en parole et de parole en texte.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 95682612b4b0fdb1baa5038039630e74abddb1a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890466"
---
# <a name="what-is-speech-translation"></a>Quelle est la traduction vocale ?

Traduction vocale à partir des Services de reconnaissance vocale de Azure, permet la traduction vocale vocale et de reconnaissance vocale en temps réel, multilingue des flux audio. Le Speech SDK, vos applications, les outils et les appareils d’accéder à des transcriptions de source et les sorties de la traduction d’audio fourni. Les résultats intermédiaires de transcription et de traduction sont retournés comme la voix est détectée, et les résultats de la finale peuvent être convertis en la synthèse vocale.

Moteur de traduction de Microsoft repose sur deux approches différentes : traduction automatique statistique (SMT) et traduction automatique NEURONALE (NMT). SMT utilise une analyse statistique avancée pour estimer les meilleures traductions possible compte tenues du contexte de quelques mots. Avec NMT, les réseaux neuronaux sont utilisés pour fournir des traductions plus précises et naturel en utilisant le contexte complet de phrases traduire des mots.

Aujourd'hui, Microsoft utilise NMT de traduction à la plupart des langages. Tous les [langues disponibles pour la traduction de parole en parole](language-support.md#speech-translation) sont alimentées par la NMT. La traduction de parole en texte peut utiliser la SMT ou la NMT en fonction de la paire de langues. Lorsque le langage cible est pris en charge par NMT, la traduction complète est optimisé pour NMT. Lorsque la langue cible n’est pas pris en charge par NMT, la traduction est un mélange de NMT et SMT, l’anglais comme un « pivot » entre les deux langages.

## <a name="core-features"></a>Fonctionnalités de base

Voici les fonctionnalités disponibles via le Speech SDK et des API REST :

| Cas d’utilisation | Kit SDK  | REST |
|----------|-----|------|
| Traduction de la parole-texte avec les résultats de la reconnaissance. | Oui | Non  |
| Traduction de la reconnaissance vocale vocale. | Oui | Non  |
| Résultats de reconnaissance et de traduction intermédiaires. | Oui | Non  |

## <a name="get-started-with-speech-translation"></a>Bien démarrer avec la traduction vocale

Nous proposons des guides de démarrage rapide conçu pour que vous exécutez le code en moins de 10 minutes. Cette table comprend une liste des Démarrages rapides de traduction vocale organisés par langage.

| Démarrage rapide | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET framework](quickstart-translate-speech-dotnetframework-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) |  Windows | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exemple de code

Exemple de code pour le Speech SDK est disponible sur GitHub. Ces exemples couvrent des scénarios courants tels que la lecture audio à partir d’un fichier ou le flux, continue et coup reconnaissance/traduction et l’utilisation des modèles personnalisés.

* [Exemples de la parole-texte et de traduction (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guides de migration

> [!WARNING]
> Translator Speech sera désactivé le 15 octobre 2019.

Si vos applications, des outils ou produits utilisez Translator Speech, nous avons créé des guides pour vous aider à migrer vers les Services de reconnaissance vocale.

* [Migrer à partir de l’API Translator Speech vers les Services de reconnaissance vocale](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [SDK Speech Devices](speech-devices-sdk.md)
* [API REST : Parole-texte](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Personnalisation et la transcription de lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une clé d’abonnement Services de reconnaissance vocale gratuitement](get-started.md)
* [Obtenir la Kit de développement logiciel de reconnaissance vocale](speech-sdk.md)
