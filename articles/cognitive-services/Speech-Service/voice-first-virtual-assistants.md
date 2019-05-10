---
title: Personnalisé voix en premier virtuels assistants (version préliminaire) - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble de fonctionnalités, des fonctionnalités et des restrictions pour les assistants personnalisés voix en premier virtuels à l’aide du canal Direct ligne vocale sur le Bot Framework et le Kit de développement logiciel Services Speech (SDK) Cognitive.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 1344de526564ab623a51eb903951b6a2e866048d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523487"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Sur les assistants virtuel personnalisé voix en premier aperçu

Les assistants virtuels personnalisés qui utilisent Azure Speech Services permettent aux développeurs de créer des interfaces conversationnelles naturelles pour leurs applications et leurs expériences. Le canal Direct Line Speech de Bot Framework améliore ces fonctionnalités en fournissant un point d’entrée orchestré et coordonné à un bot compatible qui permet des interactions vocales entrantes et sortantes avec une faible latence et une grande fiabilité. Ces robots peuvent utiliser la reconnaissance vocale (LUIS de Microsoft) pour l’interaction en langage naturel. Reconnaissance vocale de ligne directe est accessible par les appareils à l’aide du Kit de développement logiciel (SDK) de reconnaissance vocale.

   ![Diagramme conceptuel d’un flux de service par ligne directe vocale d’orchestration](media/voice-first-virtual-assistants/overview.png "flux de la chaîne de reconnaissance vocale")

Vocale de ligne directe et ses fonctionnalités pour les assistants de virtuels orienté voix personnalisées sont un supplément idéale pour le [Solution Compagnon virtuelle](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction) et [modèle d’entreprise](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction). Bien que vocale de ligne directe peut fonctionner avec un robot quelconques compatible, ces ressources fournissent une ligne de base réutilisable pour des expériences de haute qualité conversationnelles, ainsi que des compétences de prise en charge courantes et modèles pour la mise en route rapide.

## <a name="core-features"></a>Fonctionnalités de base

| Catégorie | Caractéristiques |
|----------|----------|
|[Mise en éveil personnalisé word](speech-devices-sdk-create-kws.md) | Vous pouvez autoriser les utilisateurs à commencer des conversations avec des robots à l’aide d’un mot clé personnalisé comme « Hey Contoso. » Cette tâche est réalisée par un moteur de word personnalisé de mise en éveil dans le Speech SDK, qui peut être configuré avec un mot de mise en éveil personnalisé [que vous pouvez générer ici](speech-devices-sdk-create-kws.md). Le canal Direct vocale ligne inclut la vérification de mot de mise en éveil du côté service qui améliore la précision de l’activation de word de mise en éveil par rapport à l’appareil uniquement.
|[Reconnaissance vocale](speech-to-text.md) | Le canal Direct vocale ligne inclut la transcription audio en temps réel dans à l’aide du texte reconnu [parole-texte](speech-to-text.md) à partir des Services de reconnaissance vocale de Azure. Ce texte est disponible pour votre robot et votre application cliente comme il est de transcription.
|[Conversion de texte par synthèse vocale](text-to-speech.md) | Réponses textuelles à partir de votre robot seront synthétisés à l’aide de [synthèse vocale](text-to-speech.md) à partir des Services de reconnaissance vocale de Azure. Cette synthèse puis sera disponible pour votre application cliente comme un flux audio. Microsoft offre la possibilité de créer votre propre voix TTS neuronal personnalisé et de haute qualité qui offre une voix à votre marque, pour en savoir plus [contactez-nous](mailto:mstts@microsoft.com).
|[Reconnaissance vocale par ligne directe](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | En tant que canal dans le cadre de robot, vocale de ligne directe permet une connexion fluide et homogène entre votre application cliente, un robot compatible et les fonctionnalités des Services de reconnaissance vocale de Azure. Pour plus d’informations sur la configuration de votre robot pour utiliser le canal Direct vocale de ligne, consultez [sa page dans la documentation de Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Exemple de code

Exemple de code pour la création d’un assistant virtuel vocal en premier est disponible sur GitHub. Ces exemples couvrent l’application cliente pour se connecter à votre robot dans plusieurs langages de programmation populaires.

* [Exemples de l’assistant virtuel vocal en premier (SDK)](https://aka.ms/csspeech/samples)
* [Démarrage rapide : voix en premier les assistants virtuels (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Démarrage rapide : assistants virtuels voix en premier (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Personnalisation

Voix en premier les assistants virtuels créés à l’aide des Services de reconnaissance vocale Azure peuvent utiliser la gamme complète des options de personnalisation disponibles pour [parole-texte](speech-to-text.md), [synthèse vocale](text-to-speech.md), et [mot clé personnalisée sélection](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Options de personnalisation varient selon la langue/région (voir [langues prises en charge](supported-languages.md)).

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [Service de robot Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
* [Obtenir la Kit de développement logiciel de reconnaissance vocale](speech-sdk.md)
* [Créer et déployer un bot de base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obtenir la Solution de l’Assistant virtuelle et le modèle d’entreprise](https://github.com/Microsoft/AI)
