---
title: Parole-texte avec les Services Azure de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Parole-texte à partir des Services Azure de reconnaissance vocale, également appelé parole-texte, permet la transcription en temps réel de flux audio en texte pouvant consommer les applications, outils ou bien vos appareils, afficher et agir en tant qu’entrée de commande. Ce service est alimenté par la même technologie de reconnaissance que Microsoft utilise pour Cortana et les produits Office et fonctionne de façon transparente avec la traduction et la synthèse vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 7596670e794c090b04f81cf6b235a4bc54c1f3c4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800098"
---
# <a name="what-is-speech-to-text"></a>Quelle est la parole-texte ?

Parole-texte à partir des Services Azure de reconnaissance vocale, également appelé parole-texte, permet la transcription en temps réel de flux audio en texte pouvant consommer les applications, outils ou bien vos appareils, afficher et agir en tant qu’entrée de commande. Ce service est alimenté par la même technologie de reconnaissance que Microsoft utilise pour Cortana et les produits Office et fonctionne de façon transparente avec la traduction et la synthèse vocale.  Pour obtenir une liste complète des langues de reconnaissance vocale disponibles, consultez [langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Par défaut, le service de reconnaissance vocale utilise le modèle de langage universelles. Ce modèle a été formé à l’aide de données appartenant à Microsoft et est déployé le cloud. Il est optimale pour conversationnel et scénarios de dictée. Si vous utilisez la reconnaissance vocale pour la reconnaissance et la transcription dans un environnement unique, vous pouvez créer et former des modèles de prononciation, de langue et acoustiques personnalisés pour prendre en compte un bruit ambiant ou le vocabulaire spécifique d’un secteur. 

Vous pouvez facilement capturer des données audio à partir d’un microphone, lire à partir d’un flux de données ou accéder à des fichiers audio à partir du stockage avec le Speech SDK et des API REST. Le Speech SDK prend en charge WAV/PCM 16 bits, 8 kHz à 16 kHz, audio de canal unique pour la reconnaissance vocale. Formats audio supplémentaires sont pris en charge à l’aide de la [point de terminaison REST parole-texte](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou [transcription service batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Fonctionnalités de base

Voici les fonctionnalités disponibles via le Speech SDK et des API REST :

| Cas d’utilisation | Kit SDK  | REST |
|----------|-----|------|
| Transcrire énoncés courts (< 15 secondes). Prend uniquement en charge les résultats de la transcription finale. | Oui | Oui |
| La transcription continue d’énoncés long et de diffusion en continu de l’audio (> 15 secondes). Prend en charge les résultats intermédiaires et finaux transcription. | Oui | Non |
| Dérivent avec des résultats de reconnaissance des intentions [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Oui | Non\* |
| Traitement par lots de transcription des fichiers audio en mode asynchrone. | Non | Oui\** |
| Créer et gérer des modèles vocaux. | Non | Oui\** |
| Créer et gérer les déploiements de modèle personnalisé. | Non | Oui\** |
| Créer des tests de précision pour mesurer la précision du modèle de ligne de base par rapport à des modèles personnalisés. | Non | Oui\** |
| Gérer les abonnements. | Non | Oui\** |

\* *Les intentions et entités de LUIS peuvent être dérivées à l’aide d’un abonnement LUIS distinct. Avec cet abonnement, le Kit de développement peut appeler LUIS pour vous et fournir entité et les résultats intentionnels. L’API REST vous permet d’appeler LUIS vous-même pour dériver des intentions et des entités avec votre abonnement LUIS.*

\** *Ces services sont disponibles à l’aide du point de terminaison cris.ai. Consultez [Swagger référence](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Bien démarrer avec la parole-texte

Nous proposons des démarrages rapides pour la plupart des langages de programmation populaires, conçus pour que votre code soit opérationnel en moins de 10 minutes. Cette table comprend une liste complète des Démarrages rapides de Speech SDK organisés par langage.

| Démarrage rapide | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript, Browser](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser, Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tr)  |

Si vous préférez utiliser le service REST de reconnaissance vocale, consultez [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Didacticiels et exemples de code

Une fois que vous avez eu l’occasion d’utiliser les Services Speech, essayez notre didacticiel qui vous apprend à reconnaître les intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS.

* [Tutoriel : Effectuer une reconnaissance des intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

Exemple de code pour le Speech SDK est disponible sur GitHub. Ces exemples couvrent des scénarios courants tels que la lecture du signal audio d’un fichier ou d’un flux, la reconnaissance continue et ponctuelle, et l’utilisation de modèles personnalisés.

* [Exemples (SDK) de la parole-texte](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemples de transcription par lot (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personnalisation

Outre le modèle universel utilisé par les Services de reconnaissance vocale, vous pouvez créer des modèles de prononciation, la langue et acoustiques personnalisés spécifiques à votre expérience. Voici une liste des options de personnalisation :

| Modèle | Description  |
|-------|-------------|
| [Modèle acoustique](how-to-customize-acoustic-models.md) | Création d’un modèle acoustique personnalisé est utile si votre application, des outils ou les appareils sont utilisés dans un environnement spécifique, comme dans une voiture ou une fabrique avec les conditions d’enregistrement spécifique. Par exemple, vous pouvez avoir à traiter des façons de parler avec un accent, des bruits de fond spécifiques ou l’utilisation d’un microphone spécifique pour l’enregistrement. |
| [Modèle de langage](how-to-customize-language-model.md) | Créez un modèle de langage personnalisé afin d’améliorer la transcription du vocabulaire ou de la grammaire spécifiques d’un secteur, par exemple, la terminologie médicale ou le jargon informatique. |
| [Modèle de prononciation](how-to-customize-pronunciation.md) | Avec un modèle de prononciation personnalisé, vous pouvez définir la forme phonétique et écrite d’un mot. Cela peut être utile pour traiter les termes personnalisés que sont notamment les noms et les acronymes. Pour commencer, vous n’avez besoin que d’un fichier de prononciation (un simple fichier .txt). |

> [!NOTE]
> Options de personnalisation varient selon la langue/région (voir [langues prises en charge](supported-languages.md)).

## <a name="migration-guides"></a>Guides de migration

> [!WARNING]
> Reconnaissance vocale Bing sera désactivé le 15 octobre 2019.

Si vos applications, des outils ou produits sont à l’aide de l’API reconnaissance vocale Bing ou la reconnaissance vocale personnalisé, nous avons créé des guides pour vous aider à migrer vers les Services de reconnaissance vocale.

* [Migrer à partir de la reconnaissance vocale Bing pour les Services de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrer à partir de la reconnaissance vocale personnalisé pour les Services de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [SDK Speech Devices](speech-devices-sdk.md)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
* [Obtenir la Kit de développement logiciel de reconnaissance vocale](speech-sdk.md)
