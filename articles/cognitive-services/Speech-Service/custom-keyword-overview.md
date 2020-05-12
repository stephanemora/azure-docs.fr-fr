---
title: Mots clés personnalisés - Service Speech
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des fonctionnalités, caractéristiques et restrictions des mots clés personnalisés à l’aide du SDK Speech.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802134"
---
# <a name="what-is-a-keyword"></a>Qu’est-ce qu’un mot clé ?

Un mot clé est un mot ou une expression courte qui permet d’activer la fonction vocale de votre produit. Par exemple, « Hey Cortana » est le mot clé de l’assistant Cortana. Avec l’activation de la fonction vocale, vos utilisateurs peuvent commencer à interagir avec votre produit en mode mains libres, simplement en prononçant le mot clé. Comme votre produit écoute en permanence le mot clé, toutes les données audio sont traitées localement sur l’appareil de l’utilisateur jusqu’à ce qu’une détection se produise afin de garantir que ses données restent aussi privées que possible.

## <a name="core-features-of-custom-keyword"></a>Fonctionnalités principales d’un mot clé personnalisé

Avec les fonctionnalités de personnalisation, de performances et d’intégration des mots clés personnalisés, vous pouvez adapter l’activation de la fonction vocale pour mieux répondre à l’objectif de votre produit et aux besoins de vos utilisateurs.

| Fonctionnalité | Description |
|----------|----------|
| Personnalisation des mots clés | Extension de votre marque, un mot clé renforce la relation que vous avez tissée avec vos clients. Le portail des mots clés personnalisés sur Speech Studio vous permet de spécifier n’importe quel mot ou n’importe quelle expression courte qui représente le mieux votre marque. Vous pouvez personnaliser davantage votre mot clé en choisissant les prononciations appropriées, qui seront respectées par le modèle de mot clé généré.
| Vérification du mot clé | Quand le niveau de confiance du mot clé détecté localement est élevé, le contenu audio est envoyé au cloud pour vérifier plus en profondeur qu’un utilisateur a prononcé le mot clé. La vérification du mot clé fournit une couche de sécurité supplémentaire en réduisant l’impact d’une mauvaise détection locale incorrecte et en protégeant la confidentialité des utilisateurs.
| Intégration de l’assistant vocal et du SDK Speech | Les mots clés générés à partir de l’option de mot clé personnalisé sur Speech Studio peuvent être facilement intégrés à votre appareil ou application par le biais du SDK Speech. Pointez simplement le SDK vers le modèle de mot clé fourni par Speech Studio : votre produit sera activé par la voix, avec un renforcement par une vérification du mot clé. Vous pouvez effectuer des expériences commandées par la voix sur votre produit en générant votre propre [assistant vocal](voice-assistants.md).

## <a name="sample-code"></a>Exemple de code

Des exemples de code pour l’intégration d’un modèle de mot clé sont disponibles sur GitHub. Les exemples suivants couvrent l’application cliente pour l’intégration de votre mot clé à plusieurs langages de programmation populaires.

* [Tutoriel : Activer votre produit par la voix avec le SDK Speech, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Exemples d’assistant vocal (Kit de développement logiciel [SDK])](https://aka.ms/csspeech/samples)

## <a name="tutorial"></a>Didacticiel

* Guide pratique pour [créer un mot clé personnalisé avec Speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
* Guide pratique pour [activer votre produit par la voix avec le SDK Speech, C#](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Documents de référence

* [Instructions de nommage des mots clés personnalisés](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir gratuitement une clé d’abonnement au service Speech](get-started.md)
* [Créer un mot clé personnalisé](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [Obtenir le kit SDK Speech](speech-sdk.md)
* [En savoir plus sur les assistants vocaux](voice-assistants.md)
