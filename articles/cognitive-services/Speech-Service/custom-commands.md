---
title: Commandes personnalisées (préversion) - Service Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités et des restrictions relatives à Commandes personnalisées (préversion), une solution permettant de créer des assistants vocaux.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: dd3e27aefe03ee30e721fcbb915d9ad4b3821618
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806301"
---
# <a name="custom-commands-preview"></a>Commandes personnalisées (préversion)

Les [assistants vocaux](voice-assistants.md) écoutent les utilisateurs et réagissent en prenant une mesure, souvent en parlant. Ils utilisent la [reconnaissance vocale](speech-to-text.md) pour transcrire la parole de l’utilisateur, puis agissent sur la compréhension du langage naturel du texte. Cette action comprend fréquemment une parole de l’assistant générée à l’aide de la [synthèse vocale](text-to-speech.md). Les appareils se connectent aux assistants avec l’objet `DialogServiceConnector` du kit de développement logiciel (SDK) Speech.

**Commandes personnalisées (préversion)** est une solution rationalisée permettant de créer un assistant vocal. Elle propose une expérience de création unifiée, un modèle d’hébergement automatique et une complexité relativement inférieure par rapport aux autres options de création d’assistant, comme [Direct Line Speech](direct-line-speech.md). Cette simplification, toutefois, s’accompagne d’une réduction de la flexibilité. Ainsi, la solution Commandes personnalisées (préversion) est la plus adaptée à la réalisation de tâches ou aux scénarios de commande et de contrôle. Il est particulièrement bien adapté pour les appareils IoT et sans périphérique de contrôle.

Pour une interaction et une intégration conversationelles complexes avec d’autres solutions, telles que [Virtual Assistant Solution et Enterprise Template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) il est recommandé d’utiliser Direct Line Speech.

Les outils adaptés à la solution Commandes personnalisées (préversion) disposent d’un vocabulaire fixe avec des ensembles de variables bien définis. Par exemple, les tâches d’automatisation domestiques, telles que le contrôle d’un thermostat, sont idéales.

   ![Exemples de scénarios de réalisation de tâches](media/voice-assistants/task-completion-examples.png "Exemple de réalisation de tâches")

## <a name="getting-started-with-custom-commands-preview"></a>Prise en main de la solution Commandes personnalisées (préversion)

La première étape liée à l’utilisation de la solution Commandes personnalisées (préversion) pour créer un assistant vocal consiste à [obtenir une clé d’abonnement vocale](get-started.md) et à accéder au Générateur de commandes personnalisées (préversion) sur [Speech Studio](https://speech.microsoft.com). À partir de là, vous pouvez créer une application Commandes personnalisées (préversion) et la publier. Une fois cette opération effectuée, une application se trouvant sur un appareil peut communiquer avec elle à l’aide du kit de développement logiciel (SDK) Speech.

   ![Création d’un flux pour Commandes personnalisées (préversion)](media/voice-assistants/custom-commands-flow.png "Flux de création de la solution Commandes personnalisées (préversion)")

Nous proposons des guides de démarrage rapide conçus pour vous permettre d’exécuter du code en moins de 10 minutes.

* [Créer une application Commandes personnalisées (préversion)](quickstart-custom-speech-commands-create-new.md)
* [Créer une application Commandes personnalisées (préversion) avec des paramètres](quickstart-custom-speech-commands-create-parameters.md)
* [Se connecter à une application Commandes personnalisées (préversion) avec le kit de développement logiciel (SDK) Speech,C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Exemple de code

Un exemple de code pour la création d’un assistant vocal avec la solution Commandes personnalisées (préversion) est disponible sur GitHub.

* [Exemples d’assistant vocal (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Personnalisation

Les assistants vocaux créés à l’aide du service Speech peuvent utiliser la gamme complète des options de personnalisation disponibles pour la [reconnaissance vocale](speech-to-text.md), la [synthèse vocale](text-to-speech.md) et la [sélection de mot clé personnalisé](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Les options de personnalisation varient selon la langue/paramètres régionaux (voir [Langues prises en charge](supported-languages.md)).

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
* [Obtenir le kit SDK Speech](speech-sdk.md)
