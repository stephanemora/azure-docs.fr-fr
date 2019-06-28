---
title: Personnaliser l’assistant virtuel « voice-first » (préversion) - Services Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités et des restrictions pour les assistants virtuels « voice-first » personnalisés utilisant le canal Direct Line Speech sur Bot Framework et le SDK Cognitive Services Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 1c5712fa8bbdb158992127f8f48d810a0a9b6f79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603483"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>À propos de la préversion des assistants virtuels « voice-first »

Les assistants virtuels personnalisés qui utilisent Azure Speech Services permettent aux développeurs de créer des interfaces conversationnelles naturelles pour leurs applications et leurs expériences. Le canal Direct Line Speech de Bot Framework améliore ces fonctionnalités en fournissant un point d’entrée orchestré et coordonné à un bot compatible qui permet des interactions vocales entrantes et sortantes avec une faible latence et une grande fiabilité. Ces bots peuvent utiliser LUIS (Language Understanding) de Microsoft pour une interaction en langage naturel. Les appareils accèdent à Direct Line Speech via le SDK Speech.

   ![Diagramme conceptuel du flux de service d’orchestration Direct Line Speech](media/voice-first-virtual-assistants/overview.png "Flux du canal Speech")


Direct Line Speech et ses fonctionnalités pour les assistants virtuels « voice first » personnalisés sont un supplément idéal pour le [modèle Virtual Assistant Solution and Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Bien que Direct Line Speech puisse fonctionner avec n’importe quel bot compatible, ces ressources fournissent une ligne de base réutilisable pour des expériences conversationnelles de haute qualité, ainsi que des compétences et des modèles sous-jacents communs pour un démarrage rapide.


## <a name="core-features"></a>Fonctionnalités de base

| Catégorie | Caractéristiques |
|----------|----------|
|[Mot déclencheur personnalisé](speech-devices-sdk-create-kws.md) | Vous pouvez permettre aux utilisateurs de commencer des conversations avec des bots à l’aide d’un mot clé personnalisé comme « Dis Contoso ». Cette tâche est réalisée avec un moteur de mot déclencheur personnalisé dans le SDK Speech, qui peut être configuré avec un mot déclencheur personnalisé [que vous pouvez générer ici](speech-devices-sdk-create-kws.md). Le canal Direct Line Speech inclut la vérification du mot déclencheur côté service qui améliore la précision de l’activation du mot déclencheur par rapport à l’appareil uniquement.
|[Reconnaissance vocale](speech-to-text.md) | Le canal Direct Line Speech inclut la transcription en temps réel du son dans du texte reconnu avec la [Reconnaissance vocale](speech-to-text.md) d’Azure Speech Services. Ce texte est disponible pour votre bot et votre application cliente lors de sa transcription.
|[Synthèse vocale](text-to-speech.md) | Les réponses textuelles de votre bot seront synthétisées à l’aide de la [Synthèse vocale](text-to-speech.md) d’Azure Speech Services. Cette synthèse sera ensuite disponible pour votre application cliente sous forme de flux audio. Microsoft offre la possibilité de créer votre propre voix TTS neuronale personnalisée et de haute qualité qui donne une voix à votre marque, pour en savoir plus [contactez-nous](mailto:mstts@microsoft.com).
|[Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | En tant que canal dans Bot Framework, Direct Line Speech permet une connexion fluide et homogène entre votre application cliente, un bot compatible et les fonctionnalités d’Azure Speech Services. Pour plus d’informations sur la configuration de votre bot pour utiliser le canal Direct Line Speech, consultez la [page appropriée dans la documentation Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Exemple de code

Un exemple de code pour la création d’un assistant virtuel « voice first » est disponible sur GitHub. Ces exemples couvrent l’application cliente pour la connexion à votre bot dans plusieurs langages de programmation populaires.

* [Exemples d’assistant virtuel « voice-first » (SDK)](https://aka.ms/csspeech/samples)
* [Démarrage rapide : assistants virtuels « voice-first » (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Démarrage rapide : assistants virtuels « voice-first » (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Personnalisation

Les assistants virtuels « voice-first » créés à l’aide d’Azure Speech Services peuvent utiliser la gamme complète des options de personnalisation disponibles pour la [reconnaissance vocale](speech-to-text.md), la [synthèse vocale](text-to-speech.md) et la [sélection de mot clé personnalisé](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Les options de personnalisation varient selon la langue/région (voir [Langues prises en charge](supported-languages.md)).

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [Service de robot Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
* [Obtenir le Kit SDK de reconnaissance vocale](speech-sdk.md)
* [Créer et déployer un bot de base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obtenir le modèle Virtual Assistant Solution and Enterprise](https://github.com/Microsoft/AI)
