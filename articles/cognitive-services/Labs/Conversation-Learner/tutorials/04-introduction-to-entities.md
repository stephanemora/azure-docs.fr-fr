---
title: Comment utiliser des entités avec un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des entités avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a42a2ec36d9ce4fb9c139dfddcde0fe0c188c888
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210350"
---
# <a name="introduction-to-entities"></a>Présentation des entités

Ce didacticiel présente les entités, les entités disqualifiantes, les entités obligatoires, ainsi que leur utilisation au sein de Conversation Learner.

## <a name="video"></a>Vidéo

[![Introduction du didacticiel sur les entités en préversion](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Configuration requise

Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution

    npm run tutorial-general

## <a name="details"></a>Détails

Les entités capturent les éléments d’informations dont le bot a besoin pour effectuer sa tâche, soit par le biais d’une extraction à partir d’énoncés d’utilisateurs ou par le biais d’une affectation par code personnalisé. Les entités proprement dites peuvent également contraindre une disponibilité d’action en étant explicitement classées comme « obligatoires » ou « disqualifiantes ».

- Les entités obligatoires doivent figurer dans la mémoire du modèle pour que l’action soit disponible
- Les entités disqualifiantes ne doivent *pas* figurer dans la mémoire du modèle pour que l’action soit disponible.

Ce didacticiel se concentre sur les entités personnalisées. Les entités préformées, à valeurs multiples, pouvant être niées et programmatiques sont abordées dans d’autres didacticiels.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Créer le modèle

1. Dans l’interface utilisateur web, cliquez sur « New Model » (Nouveau modèle).
2. Dans le champ « Name » (Nom), tapez « IntroToEntities », puis appuyez sur ENTRÉE.
3. Cliquez sur le bouton « Create » (Créer).

### <a name="entity-creation"></a>Création d’entités

1. Dans le panneau gauche, cliquez sur « Entités », puis sur le bouton « Nouvelle entité ».
2. Sous « Type d'entité », sélectionnez « Apprentissage personnalisé ».
3. Sous « Nom de l’entité », entrez « Ville ».
4. Cliquez sur le bouton « Create » (Créer).

> [!NOTE]
> Le type d’entité « Custom Trained » (Formée personnalisée) signifie que cette entité peut être formée, contrairement à d’autres types d’entités.

### <a name="create-the-actions"></a>Créer les actions

1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
2. Dans le champ « Bot’s response... » (Réponse du bot...), tapez « J’ignore quelle ville vous souhaitez ».
3. Dans le champ « Disqualifying Entities » (Entités disqualifiantes), tapez « city » (ville).
4. Cliquez sur le bouton « Create » (Créer).

> [!NOTE]
> L’ajout de l’entité « city » (ville) à « Disqualifying Entities » (Entités disqualifiantes) aurait pour effet de disqualifier cette action de la prise en compte par le robot lorsque l’entité « city » (ville) est définie dans la mémoire du bot.

À présent, créez une deuxième fonction.

1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
2. Dans le champ « Bot’s response... » (Réponse du bot), tapez « Il fait probablement beau à $city ».
3. Cliquez sur le bouton « Create » (Créer).

> [!NOTE]
> L’entité « city » (ville) a été ajoutée automatiquement à la liste des entités obligatoires par référence dans la réponse.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Former le modèle

1. Dans le volet gauche, cliquez sur « Dialogues de formation », puis sur le bouton « Nouveau dialogue de formation ».
2. Dans le panneau de chat, sous « Type your message... » (Tapez votre message...), entrez « hello ».
    - Cela simule le côté utilisateur de la conversation.
3. Cliquez sur le bouton « Sore actions » (Noter les actions).
4. Sélectionnez la réponse « J’ignore quelle ville vous souhaitez ».
5. En vous mettant à la place d’un utilisateur, répondez « Seattle ».
6. Cliquez sur le bouton « Sore actions » (Noter les actions).
7. Sélectionnez la réponse « Il fait probablement beau à $city ».
8. Cliquez sur le bouton « Save » (Enregistrer).

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entité attendue](./05-expected-entity.md)
