---
title: Reconnaissance vocale - Service Speech
titleSuffix: Azure Cognitive Services
description: La fonctionnalité de reconnaissance vocale permet la transcription en temps réel des flux audio en texte. Vos applications, outils ou appareils peuvent consommer, afficher et agir sur cette entrée de texte. Ce service fonctionne de façon continue avec les fonctionnalités de synthèse vocale et de traduction vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: e0aea7e55381e9571b156701699f5f45315bb384
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399704"
---
# <a name="what-is-speech-to-text"></a>Qu’est-ce que la reconnaissance vocale ?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

La reconnaissance vocale du service Speech permet la transcription en temps réel des flux audio en texte. Vos applications, outils ou appareils peuvent consommer, afficher et agir sur ce texte sous la forme d’une entrée de commande. Ce service s’appuie sur la même technologie de reconnaissance que celle utilisée par Microsoft pour Cortana et les produits Office. Il fonctionne de façon homogène avec les offres de service de <a href="./speech-translation.md" target="_blank">traduction<span class="docon docon-navigate-external x-hidden-focus"></span></a> et de <a href="./text-to-speech.md" target="_blank">synthèse vocale <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Vous trouverez une liste complète des langues compatibles avec la reconnaissance vocale dans [Langues prises en charge](language-support.md#speech-to-text).

Par défaut, le service de reconnaissance vocale utilise le modèle de langage universel. Ce modèle a été entraîné avec des données qui sont la propriété de Microsoft et est déployé dans le cloud. Il est particulièrement adapté aux scénarios de conversation et de dictée. Si vous utilisez la reconnaissance vocale pour la reconnaissance et la transcription dans un environnement unique, vous pouvez créer et entraîner des modèles de prononciation, de langue et d’acoustique personnalisés. La personnalisation permet de prendre en compte un bruit ambiant ou le vocabulaire spécifique d’un secteur.

> [!NOTE]
> La reconnaissance vocale Bing a été mise hors service le 15 octobre 2019. Si vos applications, outils ou produits utilisent les API Reconnaissance vocale Bing, nous avons créé des guides pour vous aider à effectuer leur migration vers le service Speech.
> - [Effectuer une migration entre la Reconnaissance vocale Bing et le service Speech](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Commencer avec la reconnaissance vocale

Le service de reconnaissance vocale est disponible via le [SDK Speech](speech-sdk.md). Plusieurs scénarios courants sont disponibles sous forme de démarrages rapides, dans différentes langues et plateformes :

 - [Démarrage rapide : Reconnaître la parole avec entrée au microphone](quickstarts/speech-to-text-from-microphone.md)
 - [Démarrage rapide : Reconnaître la voix à partir d’un fichier](quickstarts/speech-to-text-from-file.md)
 - [Démarrage rapide : Reconnaître la parole stockée dans un stockage d’objets blob](quickstarts/from-blob.md)

Si vous préférez utiliser le service REST de reconnaissance vocale, consultez [API REST](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Tutoriels et exemple de code

Une fois que vous aurez eu l’occasion d’utiliser le service Speech, essayez notre tutoriel qui vous apprendra à reconnaître les intentions d’un discours à l’aide du SDK Speech et de LUIS.

- [Tutoriel : Reconnaître les intentions d’un discours avec le SDK Speech et LUIS, en utilisant C#](how-to-recognize-intents-from-speech-csharp.md)

Un exemple de code pour le SDK Speech est disponible sur GitHub. Ces exemples couvrent des scénarios courants tels que la lecture du signal audio d’un fichier ou d’un flux, la reconnaissance continue et ponctuelle, et l’utilisation de modèles personnalisés.

- [Exemples de reconnaissance vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemples de transcription par lot (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personnalisation

En plus du modèle du service Speech standard, vous pouvez créer des modèles personnalisés. La personnalisation permet d’éliminer les obstacles à la reconnaissance vocale, comme le style d’élocution, le bruit de fond et le vocabulaire. Consultez [Custom Speech](how-to-custom-speech.md). Les options de personnalisation varient selon la langue et les paramètres régionaux. Consultez [Langues prises en charge](supported-languages.md) pour vérifier ce qui est pris en charge.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
- [Obtenir le kit SDK Speech](speech-sdk.md)
