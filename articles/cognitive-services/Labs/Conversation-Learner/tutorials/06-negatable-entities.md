---
title: Comment utiliser des entités qui peuvent être niées avec un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des entités qui peuvent être niées avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 34806803c50308a288007bf3bad129075ca8bc6d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796530"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Utiliser des entités qui peuvent être niées avec un modèle Conversation Learner

Ce didacticiel illustre la propriété « negatable » des entités.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel sur les entités Negatable](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
La propriété « Negatable » d’une entité vous permet d’étiqueter les instances normales (positives) et négatives de l’entité, de lui enseigner des modèles positifs et négatifs et d’effacer la valeur d’une entité existante. Les entités ayant un jeu de propriétés « Negatable » sont appelées entités Negatable dans Conversation Leaner.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Créer le modèle

1. Dans l’interface utilisateur web, cliquez sur « Nouveau modèle ».
2. Dans le champ « Nom », tapez « NegatableEntity » et appuyez sur entrée.
3. Cliquez sur le bouton « Créer ».

### <a name="entity-creation"></a>Création d’entités

1. Dans le volet gauche, cliquez sur « Entités », puis sur le bouton « Nouvelle entité ».
2. Sous « Type d’entité », sélectionnez « Personnalisée ».
3. Tapez « name » pour le « Nom d’entité ».
4. Cochez la case à cocher « Negatable ».
    - La vérification de cette propriété permet à l’utilisateur de fournir une valeur de l’entité ou d’indiquer qu’un élément n’est *pas* une valeur de l’entité. Dans ce cas, cela entraîne la suppression de la valeur de l’entité correspondante.
5. Cliquez sur le bouton « Créer ».

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>Créer la première action

1. Dans le volet gauche, cliquez sur Actions, puis sur le bouton New Action (Nouvelle Action).
2. Dans le champ « Bot’s response... » (Réponse du bot...), tapez « J’ignore votre nom ».
3. Dans le champ « Disqualifying Entitles » (Entités disqualifiantes), tapez « name ».
4. Cliquez sur le bouton « Créer ».

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Dans le volet gauche, cliquez sur Actions, puis sur le bouton New Action (Nouvelle Action).
2. Dans le champ « Bot’s response... » (Réponse du bot...), tapez « Je connais votre nom. Il s’agit de $name ».
3. Cliquez sur le bouton « Créer ».

> [!NOTE]
> L’entité « name » a été automatiquement ajoutée aux « Required Entities » (Entités requises) par référence dans la réponse.

Vous avez maintenant deux actions.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Dans le volet gauche, cliquez sur « Dialogues de formation », puis sur le bouton « Nouveau dialogue de formation ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Bonjour ».
3. Cliquez sur le bouton « Noter les actions ».
4. Sélectionnez la réponse « Je ne connais pas votre nom ».
    - Le centile est de 100 % (seule action valide basée sur les contraintes).
5. Dans le panneau de chat, sous Type your message... (Entrez votre message...), tapez « Mon nom est Frank ».
6. Sélectionnez « Frank » et choisissez l’étiquette « +nom »
    - Il existe deux instances de « name » : « +name » et « -name ».  (+) Signe plus ajoute ou remplace la valeur. (-) Signe moins supprime la valeur.
7. Cliquez sur le bouton « Noter les actions ».
    - L’entité « name » est désormais définie comme « Frank » dans la mémoire du modèle, par conséquent, l’action « je connais votre nom. Il s’agit de $name » est disponible.
8. Sélectionner la réponse, « je connais votre nom. Il s’agit de $name ».
9. Dans le panneau de conversation, sous Type your message... (Entrez votre message...), tapez « Mon nom n’est pas Frank ».
10. Sélectionnez « Frank », puis choisissez l’étiquette « -name »
    - Nous sélectionnons « -name » pour effacer la valeur actuelle de l’entité.
11. Cliquez sur le bouton « Noter les actions ».
12. Sélectionnez la réponse « Je ne connais pas votre nom ».
13. Dans le panneau de conversation, sous Type your message... (Entrez votre message...), tapez « Mon nom est Susan ».
14. Sélectionnez « Susan » et choisissez l’étiquette « +name »

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités à valeurs multiples](./07-multi-value-entities.md)
