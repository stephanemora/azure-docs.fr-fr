---
title: Comment utiliser des entités avec un modèle d’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les entités avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 4b1c2d9390890618a9aa61eb425fbd132917f414
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229046"
---
# <a name="introduction-to-entities"></a>Présentation des entités

Ce didacticiel présente les entités et montre comment utiliser les champs « entités disqualifiantes » et « entités requises » dans les actions.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 3](https://aka.ms/cl-tutorial-03-preview)](https://aka.ms/blis-tutorial-03)

## <a name="requirements"></a>Configuration requise

Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

Ce didacticiel illustre deux utilisations courantes des entités.  Tout d’abord, les entités peuvent extraire des sous-chaînes d’un message de l’utilisateur, par exemple l’identification d’une ville dans « quel temps fait-il à Seattle ? ».  Ensuite, les entités peuvent ajouter une contrainte lorsque des actions sont disponibles.  Plus précisément, une action peut répertorier une entité comme étant « requise » ou « disqualifiante » :
- Les entités requises d’une action doivent figurer dans la mémoire du bot pour que l’action soit disponible.
- Les entités disqualifiantes ne doivent *pas* figurer dans la mémoire du bot pour que l’action soit disponible.

D’autres didacticiels couvrent des aspects annexes des entités : entités prédéfinies, à valeurs multiples et pouvant être niées, entités par programmation et manipulation des entités dans le code.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Création du modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans Nom, entrez IntroToEntities. Cliquez ensuite sur Créer.

### <a name="create-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans la partie correspondant au nom de l’entité, entrez la ville.
3. Cliquez sur Create.

> [!NOTE]
> Le type d’entité « personnalisé », c’est-à-dire que l’entité peut être formée.  Il existe également des entités prédéfinies, ce qui signifie que leur comportement ne peut pas être ajusté. Nous les aborderons dans un autre didacticiel.

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle Action.
2. Dans Réponse, tapez « Je ne sais pas quelle ville vous recherchez ».
3. Dans Entités disqualifiantes, entrez $city. Cliquez sur Enregistrer.
    - Cela signifie que si cette entité est définie dans la mémoire du bot, cette action ne sera *pas* disponible.
2. Cliquez sur Actions, puis sur Nouvelle Action pour en créer une deuxième.
3. Dans Réponse, tapez « Il fait probablement beau à $city ».
4. Dans Required Entities (Entités requises), l’entité ville a été ajoutée automatiquement dans la mesure où elle a été désignée.
5. Cliquez sur Enregistrer.

Vous avez maintenant deux actions.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Former le bot

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « hello ».
3. Cliquez sur Score Actions (Actions de score), puis sélectionnez « Je ne sais pas quelle ville vous recherchez ».
    - La réponse dans laquelle l’entité ville est requise ne peut pas être sélectionnée, car l’entité ville n’est pas définie dans la mémoire du bot.
2. Sélectionnez « Je ne sais pas quelle ville vous recherchez ».
4. Entrez « seattle ». Mettez seattle en surbrillance, puis cliquez sur ville.
5. Cliquez sur Attribuer un score aux actions
    - La valeur ville figure maintenant dans la mémoire du bot.
    - La réponse « Il fait probablement beau à $city » est maintenant disponible. 
6. Sélectionnez « Il fait probablement beau à $city ».

Supposons qu’un utilisateur saisisse « veuillez répéter ». 
1. Saisissez cela et appuyez sur Entrée. L’entité ville et sa valeur sont en mémoire et disponibles.
2. Sélectionnez « Il fait probablement beau à $city ».

![](../media/tutorial3_entities.PNG)

Vous avez maintenant créé une entité et étiqueté les instances de celle-ci dans les messages de l’utilisateur.  Vous avez également utilisé la présence et l’absence de l’entité dans la mémoire du bot pour contrôler la disponibilité des actions par le biais des champs d’entités requises et disqualifiantes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entité attendue](./4-expected-entity.md)
