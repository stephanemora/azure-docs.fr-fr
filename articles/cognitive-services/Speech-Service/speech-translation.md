---
title: Traduction vocale avec le service Speech
titleSuffix: Azure Cognitive Services
description: Le service Speech vous permet d’ajouter à vos applications, outils et appareils la traduction entière, en temps réel et en plusieurs langues d’un contenu oral. La même API peut être utilisée pour la traduction de parole en parole et de parole en texte.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052619"
---
# <a name="what-is-speech-translation"></a>Qu’est-ce que la traduction vocale ?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

La traduction vocale du service Speech permet de traduire en temps réel les flux audio multilingues de la parole en parole et de la parole en texte. Avec le SDK Speech, vos applications, outils et appareils ont accès à une transcription de la source et à une traduction en sortie pour l’audio fourni. Des résultats de transcription et de traduction intermédiaires sont retournés dès que de la parole est détectée, et les résultats finaux peuvent être convertis en synthèse vocale.

Le moteur de traduction de Microsoft repose sur deux approches différentes : la traduction automatique statistique (SMT) et la traduction automatique neuronale (NMT). La SMT s’appuie sur une analyse statistique avancée pour estimer la meilleure traduction possible compte tenu du contexte fourni par quelques mots. Avec la NMT, le recours aux réseaux neuronaux vise à offrir une traduction plus précise et naturelle en traduisant les mots à partir du contexte complet des phrases.

Aujourd’hui, Microsoft utilise la NMT pour la traduction dans les langues les plus courantes. Tous les [langues disponibles pour la traduction de parole en parole](language-support.md#speech-translation) sont alimentées par la NMT. La traduction de parole en texte peut utiliser la SMT ou la NMT en fonction de la paire de langues. Quand la langue cible est prise en charge par la NMT, la traduction complète s’appuie sur la NMT. Quand la langue cible n’est pas prise en charge par la NMT, la traduction combine la NMT et la SMT en utilisant l’anglais comme « pivot » entre les deux langues.

## <a name="core-features"></a>Fonctionnalités de base

Les fonctionnalités disponibles via le SDK Speech et les API REST sont les suivantes :

| Cas d’utilisation | Kit SDK | REST |
|----------|-----|------|
| Traduction de la parole en texte avec les résultats de la reconnaissance. | Oui | Non |
| Traduction de la parole en parole. | Oui | Non |
| Résultats de reconnaissance et de traduction intermédiaires. | Oui | Non |

## <a name="get-started-with-speech-translation"></a>Démarrer avec la traduction vocale

Nous proposons des guides de démarrage rapide conçus pour vous permettre d’exécuter du code en moins de 10 minutes. Ce tableau comprend une liste de guides de démarrage rapide pour la traduction vocale organisés par langage.

| Démarrage rapide | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Parcourir](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Parcourir](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Parcourir](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Parcourir](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Parcourir](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exemple de code

Un exemple de code pour le SDK Speech est disponible sur GitHub. Ces exemples couvrent des scénarios courants tels que la lecture du contenu audio d’un fichier ou d’un flux, la reconnaissance/traduction continue et ponctuelle, et l’utilisation de modèles personnalisés.

* [Exemples de reconnaissance vocale et de traduction (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guides de migration

Si vos applications, outils ou produits utilisent l’[API Traduction de conversation Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), nous avons créé des guides pour vous aider à effectuer une migration vers le service Speech.

* [Effectuer une migration de l’API de traduction de conversation Translator Speech vers le service Speech](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [SDK Speech Devices](speech-devices-sdk.md)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
* [Obtenir le kit SDK Speech](speech-sdk.md)
