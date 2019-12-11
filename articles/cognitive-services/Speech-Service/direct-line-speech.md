---
title: Direct Line Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités et des restrictions des assistants vocaux utilisant Direct Line Speech avec le kit de développement logiciel (SDK) Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: cec3131d791d591375fd87d1c080294c9034a815
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806216"
---
# <a name="about-direct-line-speech"></a>À propos de Direct Line Speech

Les [assistants vocaux](voice-assistants.md) écoutent les utilisateurs et réagissent en prenant une mesure, souvent en parlant. Ils utilisent la [reconnaissance vocale](speech-to-text.md) pour transcrire la parole de l’utilisateur, puis agissent sur la compréhension du langage naturel du texte. Cette action comprend fréquemment une parole de l’assistant générée à l’aide de la [synthèse vocale](text-to-speech.md). Les appareils se connectent aux assistants avec l’objet `DialogServiceConnector` du kit de développement logiciel (SDK) Speech.

**Direct Line Speech** est une solution de bout en bout robuste permettant de créer un assistant vocal flexible et extensible, alimenté par le Bot Framework et son canal Direct Line Speech, qui est optimisé pour l'interaction voix en ligne avec les bots.

Direct Line Speech offre les plus hauts niveaux de personnalisation et de sophistication pour les assistants vocaux. Il est bien adapté aux scénarios de conversation qui sont à la fois ouverts, naturels ou hybrides, avec l’achèvement des tâches ou l’utilisation des commandes et des contrôles. Ce degré élevé de flexibilité s'accompagne d'une plus grande complexité, et les scénarios qui sont adaptés à des tâches bien définies à l’aide du langage naturel peuvent envisager d’utiliser [Custom Commands (préversion)](custom-commands.md) pour une expérience de solution simplifiée.

## <a name="getting-started-with-direct-line-speech"></a>Prise en main de Direct Line Speech

Les premières étapes de création d’un assistant vocal à l’aide de Direct Line Speech consiste à [obtenir une clé d’abonnement de reconnaissance vocale](get-started.md), créer un bot associé à cet abonnement et à connecter le bot au canal Direct Line Speech.

   ![Diagramme conceptuel du flux de service d’orchestration Direct Line Speech](media/voice-assistants/overview-directlinespeech.png "Flux du canal Speech")

Pour un guide complet et détaillé sur la création d’un assistant vocal simple utilisant Direct Line Speech, veuillez consulter le [didacticiel sur l’activation de la reconnaissance vocale de votre robot avec le kit de développement logiciel (SDK) Speech et le canal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

Nous proposons également des guides de démarrage rapide conçus pour vous permettre d’exécuter du code en moins de 10 minutes. Ce tableau comprend une liste de guides de démarrage rapide pour les assistants organisés par langage.

| Démarrage rapide | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exemple de code

Un exemple de code pour la création d’un assistant vocal est disponible sur GitHub. Ces exemples couvrent l’application cliente pour la connexion à votre assistant dans plusieurs langages de programmation populaires.

* [Exemples d’assistant vocal (SDK)](https://aka.ms/csspeech/samples)
* [Tutoriel : Activer les fonctions vocales sur votre assistant avec le SDK Speech, C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personnalisation

Les assistants vocaux créés à l’aide du service Speech peuvent utiliser la gamme complète des options de personnalisation disponibles pour la [reconnaissance vocale](speech-to-text.md), la [synthèse vocale](text-to-speech.md) et la [sélection de mot clé personnalisé](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Les options de personnalisation varient selon la langue/paramètres régionaux (voir [Langues prises en charge](supported-languages.md)).

Direct Line Speech et ses fonctionnalités pour les assistants vocaux sont un supplément idéal pour le [modèle Virtual Assistant Solution and Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Bien que Direct Line Speech puisse fonctionner avec n’importe quel bot compatible, ces ressources fournissent une ligne de base réutilisable pour des expériences conversationnelles de haute qualité, ainsi que des compétences et des modèles sous-jacents communs pour un démarrage rapide.

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [Service de robot Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
* [Obtenir le kit SDK Speech](speech-sdk.md)
* [Créer et déployer un bot de base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obtenir le modèle Virtual Assistant Solution and Enterprise](https://github.com/Microsoft/AI)
