---
title: Assistants vocaux - Service Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités et des restrictions des assistants vocaux utilisant le kit de développement logiciel (SDK) Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: d687d1d353c1734c5d98121f658003afde2eb182
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812912"
---
# <a name="about-voice-assistants"></a>À propos des assistants vocaux

Les assistants vocaux qui utilisent le service Speech permettent aux développeurs de créer des interfaces conversationnelles naturelles pour leurs applications et leurs expériences.

Le service d’assistant vocal permet une interaction rapide et fiable entre un appareil et une implémentation d’assistant qui utilise soit (1) le canal Direct Line Speech de Bot Framework, soit (2) le service intégré Custom Commands (préversion) pour réaliser la tâche.

Les applications se connectent au service de l’assistant vocal avec le kit de développement logiciel (SDK) Speech.

   ![Diagramme conceptuel du flux du service d’orchestration de l’assistant vocal](media/voice-assistants/overview.png "Flux de l’assistant vocal")

## <a name="choosing-an-assistant-solution"></a>Choix d’une solution d’Assistant

La première étape de création d’un Assistant vocal est de décider de ce qu’il doit faire. Le service Speech fournit plusieurs solutions complémentaires pour la création des interactions de votre Assistant. Que vous vouliez la flexibilité et la polyvalence du canal [Direct Line Speech](direct-line-speech.md) de Bot Framework ou la simplicité des [commandes personnalisées (préversion)](custom-commands.md) pour des scénarios simples, sélectionner les bons outils vous aidera à démarrer.

| Si vous voulez... | Envisagez... | Par exemple... |
|-------------------|------------------|----------------|
|Conversation libre avec une intégration de compétences robustes et un contrôle complet du déploiement | Le canal [Direct Line Speech](direct-line-speech.md) de Bot Framework | <ul><li>« Je dois aller à Seattle »</li><li>« Quel genre de pizza puis-je commander ? »</li></ul>
|Conversation de commande et de contrôle ou conversation axée sur des tâches avec création et hébergement simplifiés | [Custom Commands (préversion)](custom-commands.md) | <ul><li>« Allumer le plafonnier »</li><li>« Augmenter la température de 5 degrés »</ul>

Nous recommandons [Direct Line Speech](direct-line-speech.md) comme meilleur choix par défaut si vous n’êtes pas encore sûr de ce que votre Assistant doit traiter. Il offre une intégration avec un ensemble complet d’outils et d’aides à la création, comme le [modèle de solution d’Assistant virtuel et d’entreprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) et le [service QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) pour créer des modèles courants et utiliser vos sources de connaissances existantes.

[Commandes personnalisées (préversion)](custom-commands.md) offre une expérience de création et d’hébergement simplifiée, spécialement adaptée aux scénarios de commande et de contrôle en langage naturel.

   ![Comparaison des solutions Assistant](media/voice-assistants/assistant-solution-comparison.png "Comparaison des solutions Assistant")

## <a name="core-features"></a>Fonctionnalités de base

Que vous choisissiez [Direct Line Speech](direct-line-speech.md) ou [Commandes personnalisées (préversion)](custom-commands.md) pour créer les interactions de votre Assistant, vous pouvez utiliser un ensemble complet de fonctionnalités de personnalisation pour personnaliser votre Assistant avec votre marque, votre produit et votre personnalité.

| Category | Caractéristiques |
|----------|----------|
|[Mot clé personnalisé](speech-devices-sdk-create-kws.md) | Les utilisateurs peuvent engager des conversations avec les assistants au moyen de mots clés personnalisés, tels que « Bonjour Contoso ». Une application effectue cette action avec un moteur de mot clé personnalisé dans le SDK Speech, qui peut être configuré avec un mot déclencheur personnalisé [que vous pouvez générer ici](speech-devices-sdk-create-kws.md). Les assistants vocaux peuvent utiliser la vérification des mots clés côté service pour améliorer la précision de l’activation des mots clés (par rapport à l’appareil seul).
|[Reconnaissance vocale](speech-to-text.md) | Les assistants vocaux convertissent en temps réel le contenu audio en texte écrit à l’aide de la [Reconnaissance vocale](speech-to-text.md) du service Speech. Lors de sa transcription, ce texte est disponible pour l’implémentation de votre assistant et votre application cliente.
|[Synthèse vocale](text-to-speech.md) | Les réponses textuelles de votre assistant seront synthétisées à l’aide de la [Synthèse vocale](text-to-speech.md) du service Speech. Cette synthèse est ensuite disponible pour votre application cliente sous forme de flux audio. Microsoft offre la possibilité de créer votre propre voix TTS neuronale personnalisée et de haute qualité qui donne une voix à votre marque. Pour en savoir plus, [contactez-nous](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Prise en main des assistants vocaux

Nous proposons des guides de démarrage rapide conçus pour vous permettre d’exécuter du code en moins de 10 minutes. Ce tableau comprend une liste de guides de démarrage rapide pour les assistants organisés par langage.

| Démarrage rapide | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exemple de code

Un exemple de code pour la création d’un assistant vocal est disponible sur GitHub. Ces exemples couvrent l’application cliente pour la connexion à votre assistant dans plusieurs langages de programmation populaires.

* [Exemples d’assistant vocal (SDK)](https://aka.ms/csspeech/samples)
* [Tutoriel : Activer les fonctions vocales sur votre assistant avec le SDK Speech, C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Didacticiel

Un tutoriel sur l’[activation des fonctions vocales dans votre assistant à l’aide du SDK Speech et du canal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personnalisation

Les assistants vocaux créés à l’aide du service Speech peuvent utiliser la gamme complète des options de personnalisation disponibles pour la [reconnaissance vocale](speech-to-text.md), la [synthèse vocale](text-to-speech.md) et la [sélection de mot clé personnalisé](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Les options de personnalisation varient selon la langue/paramètres régionaux (voir [Langues prises en charge](supported-languages.md)).

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [Service de robot Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
* [Obtenir le kit SDK Speech](speech-sdk.md)
* [En savoir plus sur Custom Commands (préversion)](custom-commands.md)
* [En savoir plus sur Direct Line Speech](direct-line-speech.md)
