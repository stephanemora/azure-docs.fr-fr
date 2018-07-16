---
title: Comment utiliser les entités avec une application Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les entités avec une application Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369589"
---
# <a name="introduction-to-entities"></a>Présentation des entités

Ce didacticiel présente les entités et montre comment utiliser les champs « entités disqualifiantes » et « entités requises » dans les actions.

## <a name="requirements"></a>Configuration requise

Vous devez exécuter le bot didacticiel général pour utiliser ce didacticiel.

    npm run tutorial-general

## <a name="details"></a>Détails

Ce didacticiel illustre deux utilisations courantes des entités.  Tout d’abord, les entités peuvent extraire des sous-chaînes d’un message de l’utilisateur, par exemple l’identification d’une ville dans « quel temps fait-il à Seattle ? ».  Ensuite, les entités peuvent ajouter une contrainte lorsque des actions sont disponibles.  Plus précisément, une action peut répertorier une entité comme étant « requise » ou « disqualifiante » :
- Les entités requises d’une action doivent figurer dans la mémoire du bot pour que l’action soit disponible.
- Les entités disqualifiantes ne doivent *pas* figurer dans la mémoire du bot pour que l’action soit disponible.

D’autres didacticiels couvrent des aspects annexes des entités : entités prédéfinies, à valeurs multiples et pouvant être niées, entités par programmation et manipulation des entités dans le code.

## <a name="steps"></a>Étapes

### <a name="create-the-application"></a>Création de l'application

1. Dans l’interface utilisateur web, cliquez sur Nouvelle application.
2. Dans Nom, entrez IntroToEntities. Cliquez ensuite sur Créer.

### <a name="create-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans la partie correspondant au nom de l’entité, entrez la ville.
3. Click Create

Notez le type d’entité « personnalisé », c’est-à-dire que l’entité peut être formée.  Il existe également des entités prédéfinies, ce qui signifie que leur comportement ne peut pas être ajusté. Nous les aborderons dans un autre didacticiel.

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle Action.
2. Dans Réponse, tapez « Je ne sais pas quelle ville vous recherchez ».
3. Dans Disqualifying Entities (Entités disqualifiantes), entrez $city. Cliquez sur Enregistrer.
    - Cela signifie que si cette entité est définie dans la mémoire du bot, cette action ne sera *pas* disponible.
2. Cliquez sur Actions, puis sur Nouvelle Action pour en créer une deuxième.
3. Dans Réponse, tapez « Il fait probablement beau à $city ».
4. Dans Required Entities (Entités requises), notez que l’entité ville a été ajoutée automatiquement dans la mesure où elle a été désignée.
5. Cliquez sur Enregistrer.

Vous avez maintenant deux actions.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Former le bot

1. Cliquez sur Train Dialogs (Dialogues d’apprentissage), puis sur Nouveau dialogue d’apprentissage.
2. Tapez « hello ».
3. Cliquez sur Score Actions (Actions de score), puis sélectionnez « Je ne sais pas quelle ville vous recherchez ».
    - Notez que la réponse dans laquelle l’entité ville est requise ne peut pas être sélectionnée, car l’entité ville n’est pas définie dans la mémoire du bot.
2. Sélectionnez « Je ne sais pas quelle ville vous recherchez ».
4. Entrez « seattle ». Mettez seattle en surbrillance, puis cliquez sur ville.
5. Cliquez sur (Score Actions) Actions de score.
    - Veuillez noter que la valeur ville figure maintenant dans la mémoire du bot.
    - La réponse « Il fait probablement beau à $city » est maintenant disponible. 
6. Sélectionnez « Il fait probablement beau à $city ».

Supposons qu’un utilisateur saisisse « veuillez répéter ». 
1. Saisissez cela et appuyez sur Entrée. Notez que l’entité ville et sa valeur sont en mémoire et disponibles.
2. Sélectionnez « Il fait probablement beau à $city ».

![](../media/tutorial3_entities.PNG)

Vous avez maintenant créé une entité et étiqueté les instances de celle-ci dans les messages de l’utilisateur.  Vous avez également utilisé la présence et l’absence de l’entité dans la mémoire du bot pour contrôler la disponibilité des actions par le biais des champs d’entités requises et disqualifiantes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entité attendue](./4-expected-entity.md)
