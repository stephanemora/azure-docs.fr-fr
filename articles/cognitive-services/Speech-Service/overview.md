---
title: Qu’est-ce que le service de reconnaissance vocale ?
titleSuffix: Azure Cognitive Services
description: Le service Speech rassemble la reconnaissance vocale, la synthèse vocale et la traduction vocale dans un seul abonnement Azure. Ajoutez les services Speech à vos applications, outils et appareils avec le SDK Speech, le SDK Speech Devices ou les API REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 43efc770f3a3b29a1b0fc214d2927c8c153dff96
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391636"
---
# <a name="what-is-the-speech-service"></a>Qu’est-ce que le service de reconnaissance vocale ?

Le service Speech réunit la reconnaissance vocale, la synthèse vocale et la traduction vocale dans un même abonnement Azure. Vous pouvez aisément activer vos applications, outils et appareils pour les services Speech avec le [Kit de développement logiciel (SDK) Speech](speech-sdk-reference.md), le [Kit de développement logiciel (SDK) Speech Devices](https://aka.ms/sdsdk-quickstart) ou des [API REST](rest-apis.md).

> [!IMPORTANT]
> Le service Speech remplace l’API Reconnaissance vocale Bing et Traduction de conversation Translator Speech. Pour obtenir des instructions de migration, voir _Guides pratiques > Migration_.

Ces fonctionnalités constituent le service Speech. Pour en savoir plus sur les cas d’utilisation courants de chaque fonctionnalité, utilisez les liens figurant dans ce tableau, ou parcourez la référence de l’API.

| Service | Fonctionnalité | Description | Kit SDK | REST |
|---------|---------|-------------|-----|------|
| [Reconnaissance vocale](speech-to-text.md) | Reconnaissance vocale en temps réel | La reconnaissance vocale transcrit ou traduit en temps réel des fichiers locaux ou des flux audio en texte que vos applications, outils ou appareils peuvent consommer ou afficher. Utilisez la reconnaissance vocale avec [LUIS (Language Understanding Intelligent Service)](https://docs.microsoft.com/azure/cognitive-services/luis/) pour déduire les intentions de l’utilisateur à partir des transcriptions et agir sur des commandes vocales. | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Reconnaissance vocale par lots](batch-transcription.md) | Transcrit de manière asynchrone la reconnaissance vocale de grandes quantités de données audio stockées dans Stockage Blob Azure. En plus de convertir l’audio en texte, la reconnaissance vocale par lots permet également d’effectuer la diarisation et l’analyse des sentiments. | Non | [Oui](https://westus.cris.ai/swagger/ui/index) |
| | [Conversation multi-appareil](multi-device-conversation.md) | Connecter plusieurs appareils ou clients dans une conversation pour envoyer des messages vocaux ou textuels, avec une prise en charge simple de la transcription et de la traduction| Oui | Non |
| | [Transcription de conversation](conversation-transcription-service.md) | Permet la reconnaissance vocale en temps réel, l’identification de l’orateur et la structuration (diarisation). Il est parfait pour la transcription de rencontres en personne, avec possibilité de distinguer les orateurs. | Oui | Non |
| | [Créer des modèles vocaux personnalisés](#customize-your-speech-experience) | Si vous utilisez la reconnaissance vocale pour la reconnaissance et la transcription dans un environnement unique, vous pouvez créer et former des modèles de prononciation, de langue et acoustiques personnalisés pour prendre en compte un bruit ambiant ou le vocabulaire spécifique d’un secteur. | Non | [Oui](https://westus.cris.ai/swagger/ui/index) |
| [Synthèse vocale](text-to-speech.md) | Synthèse vocale | La synthèse vocale convertit le texte d’entrée en parole synthétisée quasi humaine avec le [langage SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). Faites votre choix parmi les voix standard et les voix neuronales (voir [Prise en charge linguistique](language-support.md)). | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Créer des voix personnalisées](#customize-your-speech-experience) | Créez des polices de voix personnalisées propres à vos marques ou produits. | Non | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traduction vocale](speech-translation.md) | Traduction vocale | La traduction vocale permet à vos applications, outils et appareils d’effectuer de la traduction multilingue en temps réel de la parole. Utilisez ce service pour la traduction de voix en voix et de voix en texte. | [Oui](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Non |
| [Assistants vocaux](voice-assistants.md) | Assistants vocaux | Les assistants vocaux qui utilisent le service Speech permettent aux développeurs de créer des interfaces conversationnelles naturelles pour leurs applications et leurs expériences. Le service d’assistant vocal permet une interaction rapide et fiable entre un appareil et une implémentation d’assistant qui utilise le canal Direct Line Speech de Bot Framework ou le service intégré Custom Commands (préversion) pour réaliser la tâche. | [Oui](voice-assistants.md) | Non |
| [Reconnaissance de l’orateur](speaker-recognition-overview.md) | Identification et vérification de l’orateur | Le service Reconnaissance de l’orateur fournit des algorithmes qui vérifient et identifient les orateurs d’après leurs caractéristiques vocales uniques. Le service Reconnaissance de l’orateur est utilisé pour répondre à la question « qui parle ? ». | Oui | [Oui](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Essayer le service Speech

Nous proposons des démarrages rapides pour la plupart des langages de programmation populaires, conçus pour que votre code soit opérationnel en moins de 10 minutes. Ce tableau répertorie les démarrages rapides les plus populaires pour chaque fonctionnalité. Utilisez le volet de navigation de gauche pour explorer les langages et plateformes supplémentaires.

| Reconnaissance vocale (SDK) | Synthèse vocale (SDK) | Traduction (SDK) |
|----------------------|----------------------|-------------------|
| [Reconnaître la parole à partir d’un fichier audio](quickstarts/speech-to-text-from-file.md) | [Synthétiser la parole vers un fichier audio](quickstarts/text-to-speech-audio-file.md) | [Traduire la reconnaissance vocale](quickstarts/translate-speech-to-text.md) |
| [Reconnaître la parole avec un micro](quickstarts/speech-to-text-from-microphone.md) | [Synthétiser la parole vers un haut-parleur](quickstarts/text-to-speech.md) | [Traduire la parole en plusieurs langues cibles](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Reconnaître la parole stockée dans un stockage de blobs](quickstarts/from-blob.md) | [Asynchroniser la synthèse pour l’audio long ](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Traduire la parole en parole](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> La reconnaissance vocale et la synthèse vocale ont également des points de terminaison REST et des démarrages rapides associés.

Une fois que vous avez pu utiliser le service Speech, essayez nos tutoriels qui vous font découvrir comment résoudre différents scénarios.

- [Tutoriel : Effectuer une reconnaissance des intentions du discours à l’aide du Kit de développement logiciel (SDK) Speech et de LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
- [Tutoriel : Activer les fonctions vocales sur votre bot avec le SDK Speech, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutoriel : Créer une application Flask pour la traduction de texte, l’analyse de sentiments et la synthèse vocale de texte traduit, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obtenir un exemple de code

Un exemple de code est disponible sur GitHub pour le service Speech. Ces exemples couvrent des scénarios courants tels que la lecture du signal audio d’un fichier ou d’un flux, la reconnaissance continue et ponctuelle, et l’utilisation de modèles personnalisés. Pour voir les exemples SDK et REST, suivez ces liens :

- [Exemples de reconnaissance vocale, de synthèse vocale et de traduction vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemples de transcription par lot (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Exemples de synthèse vocale (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Exemples d’assistant vocal (Kit de développement logiciel [SDK])](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personnaliser votre expérience de reconnaissance vocale

Le service Speech fonctionne bien avec les modèles intégrés. Cependant, vous pouvez personnaliser et optimiser l’expérience pour votre produit ou environnement. Les options de personnalisation vont de l’ajustement du modèle acoustique aux polices de voix uniques pour votre marque.

| Speech Service | Plateforme | Description |
| -------------- | -------- | ----------- |
| Reconnaissance vocale | [Discours personnalisé](https://aka.ms/customspeech) | Personnalisez les modèles de reconnaissance vocale en fonction de vos besoins et des données disponibles. Éliminez les obstacles à la reconnaissance vocale, tels que le style d’élocution, le bruit de fond et le vocabulaire. |
| Synthèse vocale | [Custom Voice](https://aka.ms/customvoice) | Créez une voix unique et reconnaissable pour vos applications de synthèse vocale, avec vos données vocales disponibles. Vous pouvez ensuite affiner le réglage des sorties vocales en ajustant tout un ensemble de paramètres vocaux. |

## <a name="reference-docs"></a>Documents de référence

- [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
- [SDK Speech Devices](speech-devices-sdk.md)
- [API REST : Reconnaissance vocale](rest-speech-to-text.md)
- [API REST : Synthèse vocale](rest-text-to-speech.md)
- [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
