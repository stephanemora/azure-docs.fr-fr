---
title: API Audio long (préversion) - Service Speech
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: ed00a9df46660cc6bfb4ec5fd9a93c80f5d6653e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815331"
---
# <a name="long-audio-api-preview"></a>API Audio long (préversion)

L’API Audio long est conçue pour la synthèse asynchrone de texte de long en parole (par exemple, livres audio). Cette API ne retourne pas d’audio synthétisé en temps réel. Au lieu de cela, vous êtes supposé interroger la ou les réponses et utiliser la ou les sorties à mesure que le service les rend disponibles. Contrairement à l’API de synthèse vocale utilisée par le Kit de développement logiciel (SDK) Speech, l’API Audio long peut créer de l’audio synthétisé de plus de 10 minutes, ce qui la rend idéale pour les éditeurs et les plateformes de contenu audio.

Autres avantages de l’API Audio long  :

* La synthèse vocale que le service retourne utilise des voix neuronales, ce qui garantit des sorties audio haute fidélité.
* Les réponses en temps réel n’étant pas prises en charge, il n’est pas nécessaire de déployer un point de terminaison vocal.

> [!NOTE]
> L’API Audio long prend désormais en charge uniquement la [voix neuronale personnalisée](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Workflow

En règle générale, lorsque vous utilisez l’API Audio long, vous envoyez un ou plusieurs fichiers texte à synthétiser, interrogez l’état, puis, si celui-ci indique une réussite, vous pouvez télécharger la sortie audio.

Le diagramme suivant donne une vue d’ensemble du workflow.

![Diagramme de workflow de l’API Audio long](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Préparer du contenu pour la synthèse

Lors de la préparation de votre fichier texte, vérifiez les points suivants :

* Il s’agit d’un texte brut (.txt) ou d’un texte SSML (.txt)
  * Pour du texte brut, chaque paragraphe est séparé en appuyant sur la touche **Entrée/Retour** ([exemple d’entrée de texte brut](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt))
  * Pour du texte SSML, chaque élément SSML est considéré comme un paragraphe. Les éléments SSML sont séparés par différents paragraphes ([exemple d’entrée de texte SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)). Pour le code de langue, voir [Langage de balisage de synthèse vocale (SSML, Speech Synthesis Markup Language)](speech-synthesis-markup.md)
* Le texte est encodé au format [UTF-8 avec marque d’ordre d’octet (BOM, Byte Order Mark)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Il contient plus de 10 000 caractères ou de 50 paragraphes
* Il s’agit d’un fichier unique, non d’un fichier zip

## <a name="audio-output-formats"></a>Formats de sortie aduio

Les formats de sortie audio suivants sont pris en charge par l’API Audio long :

> [!NOTE]
> Le format audio par défaut est riff-16 khz-16 bits-mono-pcm.

* riff-8 khz-16 bits-mono-pcm
* riff-16 khz-16 bits-mono-pcm
* riff-24 khz-16 bits-mono-pcm
* riff-48 khz-16 bits-mono-pcm
* audio-16 khz-32 kbitrate-mono-mp3
* audio-16 khz-64 kbitrate-mono-mp3
* audio-16 khz-128 kbitrate-mono-mp3
* audio-24 khz-48 kbitrate-mono-mp3
* audio-24 khz-96 kbitrate-mono-mp3
* audio-24 khz-160 kbitrate-mono-mp3

## <a name="quickstarts"></a>Démarrages rapides

Nous proposons des démarrages rapides conçus pour vous aider à exécuter correctement l’API Audio long. Ce tableau comprend une liste de démarrages rapides de l’API Audio long organisés par langue.

* [Démarrage rapide : Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Exemple de code
Un exemple de code pour l’API Audio long est disponible sur GitHub.

* [Exemple de code : Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Exemple de code : C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exemple de code : Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
