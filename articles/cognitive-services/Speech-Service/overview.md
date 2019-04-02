---
title: Que sont les services Speech Azure ?
titleSuffix: Azure Cognitive Services
description: Les services Speech Azure sont l’unification de la reconnaissance vocale, de la synthèse vocale et de la traduction vocale dans un seul abonnement Azure. Il est facile d’ajouter les services Speech à vos applications, outils et appareils avec le Kit de développement logiciel (SDK) Speech, le Kit de développement logiciel (SDK) Speech Devices ou des API REST. Ajoutez des fonctionnalités vocales à un bot conversationnel, convertissez du texte en parole dans une application de traduction, ou transcrivez de grands volumes de données d’un centre d’appels.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 06b2a5211c720d50a2f14e5fa56fa296cb80d41f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519627"
---
# <a name="what-are-the-speech-services"></a>Qu’est-ce que Speech Services ?

Les services Speech Azure sont l’unification de la reconnaissance vocale, de la synthèse vocale et de la traduction vocale dans un seul abonnement Azure. Vous pouvez aisément activer vos applications, outils et appareils pour les services Speech avec le [Kit de développement logiciel (SDK) Speech](speech-sdk-reference.md), le [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk-qsg.md) ou des [API REST](rest-apis.md).

> [!IMPORTANT]
> Les services Speech ont remplacé l’API Reconnaissance vocale Bing, Translator Speech et Custom Speech. Pour obtenir des instructions de migration, voir *Guides pratiques > Migration*.

Ces fonctionnalités constituent les services Speech Azure. Pour en savoir plus sur les cas d’utilisation courants de chaque fonctionnalité, utilisez les liens figurant dans ce tableau, ou parcourez la référence de l’API.

| de diffusion en continu | Fonctionnalité | Description | Kit SDK  | REST |
|---------|---------|-------------|-----|------|
| [Reconnaissance vocale](speech-to-text.md) | Reconnaissance vocale | La reconnaissance vocale transcrit en temps réel des flux audio en texte que vos applications, outils ou appareils peuvent utiliser ou afficher. Utilisez la reconnaissance vocale avec [LUIS (Language Understanding Intelligent Service)](https://docs.microsoft.com/azure/cognitive-services/luis/) pour déduire les intentions de l’utilisateur à partir des transcriptions et agir sur des commandes vocales. | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transcription par lot](batch-transcription.md) | La transcription par lot permet de transcrire une reconnaissance vocale asynchrone de gros volumes de données. Il s’agit d’un service basé sur REST qui utilise le même point de terminaison que la personnalisation et la gestion des modèles. | Non  | [Oui](https://westus.cris.ai/swagger/ui/index) |
| | [Personnalisation](#customize-your-speech-experience) | Si vous utilisez la reconnaissance vocale pour la reconnaissance et la transcription dans un environnement unique, vous pouvez créer et former des modèles de prononciation, de langue et acoustiques personnalisés pour prendre en compte un bruit ambiant ou le vocabulaire spécifique d’un secteur. | Non  | [Oui](https://westus.cris.ai/swagger/ui/index) |
| [Synthèse vocale](text-to-speech.md) | Synthèse vocale | La synthèse vocale convertit le texte d’entrée en parole synthétisée quasi humaine. Faites votre choix parmi les voix standard et les voix neuronales (voir [Prise en charge linguistique](language-support.md)). | Non  | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Personnalisation](#customize-your-speech-experience) | Créez des polices de voix personnalisées propres à vos marques ou produits. | Non  | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traduction vocale](speech-translation.md) | Traduction vocale | La traduction vocale permet à vos applications, outils et appareils d’effectuer de la traduction multilingue en temps réel de la parole. Utilisez ce service pour la traduction de voix en voix et de voix en texte. | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Non  |

## <a name="news-and-updates"></a>Nouveautés et mises à jour

Découvrez les nouveautés introduites dans les services Speech Azure.

* Mars 2019 - Un nouveau point de terminaison de synthèse vocale, qui retourne la liste complète des voix disponibles dans chaque région, est désormais disponible. Pour plus d’informations, consultez [Informations de référence sur l’API de synthèse vocale (REST)](rest-text-to-speech.md).
* Février 2019 : publication du Kit de développement logiciel (SDK) Speech 1.3.0 avec prise en charge de [Unity (bêta)](quickstart-csharp-unity.md). Ajout de la prise en charge de la classe `AudioInput` qui vous permet de choisir la source de diffusion en continu pour l’audio. Pour la liste complète des améliorations et problèmes connus, voir les [Notes de publication](releasenotes.md).
* Décembre 2018 : publication du Kit de développement logiciel (SDK) Speech 1.2.0 avec prise en charge de [Python](quickstart-python.md) et [Node.js](quickstart-js-node.md), ainsi que d’Ubuntu 18.04 LTS. Pour plus d’informations, voir les [Notes de publication](releasenotes.md).

## <a name="try-speech-services"></a>Essayer les services Speech

Nous proposons des démarrages rapides pour la plupart des langages de programmation populaires, conçus pour que votre code soit opérationnel en moins de 10 minutes. Ce tableau répertorie les démarrages rapides les plus populaires pour chaque fonctionnalité. Utilisez le volet de navigation de gauche pour explorer les langages et plateformes supplémentaires.

| Reconnaissance vocale (SDK) | Traduction (SDK) | Synthèse vocale (REST) |
|-------------------|-------------------|-----------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows, Linux, macOS)](quickstart-python-text-to-speech.md) |
| [Javascript (navigateur)](quickstart-js-browser.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#, .NET Core (Windows, Linux, macOS)](quickstart-dotnet-text-to-speech.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows, Linux, macOS)](quickstart-nodejs-text-to-speech.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

Une fois que vous avez eu l’occasion d’utiliser les Services Speech, essayez notre didacticiel qui vous apprend à reconnaître les intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS.

* [Tutoriel : Effectuer une reconnaissance des intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="get-sample-code"></a>Obtenir un exemple de code

Un exemple de code est disponible sur GitHub pour chacun des services Speech Azure. Ces exemples couvrent des scénarios courants tels que la lecture du signal audio d’un fichier ou d’un flux, la reconnaissance continue et ponctuelle, et l’utilisation de modèles personnalisés. Pour voir les exemples SDK et REST, suivez ces liens :

* [Exemples de reconnaissance vocale et de traduction vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemples de transcription par lot (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Exemples de synthèse vocale (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Personnaliser votre expérience de reconnaissance vocale

Les services Speech Azure fonctionnent bien avec les modèles intégrés. Cependant, vous pouvez personnaliser et optimiser l’expérience pour votre produit ou environnement. Les options de personnalisation vont de l’ajustement du modèle acoustique aux polices de voix uniques pour votre marque. Une fois que vous avez créé un modèle personnalisé, vous pouvez l’utiliser avec tous les services Speech Azure.

| Speech Service | Modèle | Description |
|----------------|-------|-------------|
| Reconnaissance vocale | [Modèle acoustique](how-to-customize-acoustic-models.md) | Créez un modèle acoustique personnalisé pour des applications, outils ou appareils utilisés dans des environnements particuliers, tels qu’une voiture ou un atelier, avec des conditions d’enregistrement spécifique. Par exemple, vous pouvez adapter l’enregistrement à des accent régionaux, à des bruits de fond spécifiques ou à l’utilisation d’un microphone particulier. |
| | [Modèle de langage](how-to-customize-language-model.md) | Créez un modèle de langage personnalisé afin d’améliorer la transcription du vocabulaire ou de la grammaire spécifiques d’un secteur, par exemple, la terminologie médicale ou le jargon informatique. |
| | [Modèle de prononciation](how-to-customize-pronunciation.md) | Avec un modèle de prononciation personnalisé, vous pouvez définir la forme phonétique et écrite d’un mot. Cela peut être utile pour traiter les termes personnalisés que sont notamment les noms et les acronymes. Pour commencer, vous n’avez besoin que d’un fichier de prononciation (un simple fichier .txt). |
| Synthèse vocale | [Police de la voix](how-to-customize-voice-font.md) | Les polices de voix personnalisées vous permettent de créer une voix unique reconnaissable entre toutes pour votre marque. Il suffit d’une petite quantité de données pour commencer. Plus vous fournirez de données, plus naturelle et humaine sonnera votre police de voix. |

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [SDK Speech Devices](speech-devices-sdk.md)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
