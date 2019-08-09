---
title: Reconnaissance vocale avec Azure Speech Services
titleSuffix: Azure Cognitive Services
description: La reconnaissance vocale d’Azure Speech Services, également appelée conversion de parole en texte, permet de transcrire en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser, afficher et manipuler sous forme d’entrée de commande. Ce service repose sur la même technologie de reconnaissance que celle utilisée par Microsoft pour Cortana et les produits Office et fonctionne de façon continue avec la traduction et la synthèse vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fcb09720d188150736dbe7da0c925f5762ff6a10
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072316"
---
# <a name="what-is-speech-to-text"></a>Qu’est-ce que la reconnaissance vocale ?

La reconnaissance vocale d’Azure Speech Services, également appelée conversion de parole en texte, permet de transcrire en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser, afficher et manipuler sous forme d’entrée de commande. Ce service repose sur la même technologie de reconnaissance que celle utilisée par Microsoft pour Cortana et les produits Office et fonctionne de façon continue avec la traduction et la synthèse vocale.  Vous trouverez une liste complète des langues compatibles avec la reconnaissance vocale dans [Langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Par défaut, le service de reconnaissance vocale utilise le modèle de langage universel. Ce modèle a été entraîné avec des données qui sont la propriété de Microsoft et est déployé dans le cloud. Il est particulièrement adapté aux scénarios de conversation et de dictée. Si vous utilisez la reconnaissance vocale pour la reconnaissance et la transcription dans un environnement unique, vous pouvez créer et former des modèles de prononciation, de langue et acoustiques personnalisés pour prendre en compte un bruit ambiant ou le vocabulaire spécifique d’un secteur. 

Vous pouvez facilement capturer du contenu audio à partir d’un microphone, lire un flux ou accéder à des fichiers audio dans un espace de stockage avec le kit SDK Speech et les API REST. Le SDK Speech prend en charge le contenu audio monocanal WAV/PCM 16 bits, 16 kHz/8 kHz pour la reconnaissance vocale. D’autres formats audio sont pris en charge en utilisant le [point de terminaison REST de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou le [service de transcription Batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Fonctionnalités de base

Les fonctionnalités disponibles via le SDK Speech et les API REST sont les suivantes :

| Cas d’utilisation | Kit SDK | REST |
|----------|-----|------|
| Transcription d’énoncés courts (< 15 secondes). Prend uniquement en charge le résultat final de la transcription. | OUI | OUI |
| Transcription continue d’énoncés longs et de streaming audio (> 15 secondes). Prend en charge les résultats de transcription intermédiaires et finaux. | OUI | Non |
| Dérivation d’intentions à partir des résultats de la reconnaissance avec [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | OUI | Non\* |
| Transcription par lots (Batch) et asynchrone des fichiers audio. | Non | Oui\** |
| Création et gestion de modèles vocaux. | Non | Oui\** |
| Création et gestion de déploiements de modèles personnalisés. | Non | Oui\** |
| Création de tests de précision pour mesurer la précision du modèle de référence par rapport aux modèles personnalisés. | Non | Oui\** |
| Gestion des abonnements. | Non | Oui\** |

\* *Les intentions et entités de LUIS peuvent être dérivées à l’aide d’un abonnement LUIS distinct. Avec cet abonnement, le SDK peut appeler LUIS à votre place et fournir des résultats d’entité et d’intention. L’API REST vous permet d’appeler LUIS vous-même pour dériver des intentions et des entités avec votre abonnement LUIS.*

\** *Ces services sont disponibles avec le point de terminaison cris.ai. Voir [Informations de référence sur Swagger](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Commencer avec la reconnaissance vocale

Nous proposons des démarrages rapides pour la plupart des langages de programmation populaires, conçus pour que votre code soit opérationnel en moins de 10 minutes. Ce tableau comprend une liste complète de guides de démarrage rapide pour le SDK Speech organisés par langage.

| Démarrage rapide | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux, macOS | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript, navigateur](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser, Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tr)  |

Si vous préférez utiliser le service REST de reconnaissance vocale, consultez [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Tutoriels et exemple de code

Une fois que vous avez eu l’occasion d’utiliser les Services Speech, essayez notre didacticiel qui vous apprend à reconnaître les intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS.

* [Tutoriel : Effectuer une reconnaissance des intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

Un exemple de code pour le SDK Speech est disponible sur GitHub. Ces exemples couvrent des scénarios courants tels que la lecture du signal audio d’un fichier ou d’un flux, la reconnaissance continue et ponctuelle, et l’utilisation de modèles personnalisés.

* [Exemples de reconnaissance vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemples de transcription par lot (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personnalisation

Outre le modèle universel utilisé par Speech Services, vous pouvez créer des modèles acoustiques, de langage et de prononciation personnalisés, propres à votre expérience. Voici une liste d’options de personnalisation :

| Modèle | Description |
|-------|-------------|
| [Modèle acoustique](how-to-customize-acoustic-models.md) | Créer un modèle acoustique personnalisé s’avère utile si vos applications, outils ou appareils sont utilisés dans un environnement particulier, par exemple dans une voiture ou une usine où les conditions d’enregistrement sont spécifiques. Par exemple, vous pouvez avoir à traiter des façons de parler avec un accent, des bruits de fond spécifiques ou l’utilisation d’un microphone spécifique pour l’enregistrement. |
| [Modèle de langage](how-to-customize-language-model.md) | Créez un modèle de langage personnalisé afin d’améliorer la transcription du vocabulaire ou de la grammaire spécifiques d’un secteur, par exemple, la terminologie médicale ou le jargon informatique. |
| [Modèle de prononciation](how-to-customize-pronunciation.md) | Avec un modèle de prononciation personnalisé, vous pouvez définir la forme phonétique et écrite d’un mot. Cela peut être utile pour traiter les termes personnalisés que sont notamment les noms et les acronymes. Pour commencer, vous n’avez besoin que d’un fichier de prononciation (un simple fichier .txt). |

> [!NOTE]
> Les options de personnalisation varient selon la langue/paramètres régionaux (voir [Langues prises en charge](supported-languages.md)).

## <a name="migration-guides"></a>Guides de migration

> [!WARNING]
> La reconnaissance vocale Bing sera mise hors service le 15 octobre 2019.

Si vos applications, outils ou produits utilisent les API Reconnaissance vocale Bing ou Custom Speech, nous avons créé des guides pour vous aider à migrer vers Speech Services.

* [Migrer de la reconnaissance vocale Bing vers Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrer de Custom Speech vers Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [SDK Speech Devices](speech-devices-sdk.md)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
* [Obtenir le kit SDK Speech](speech-sdk.md)
