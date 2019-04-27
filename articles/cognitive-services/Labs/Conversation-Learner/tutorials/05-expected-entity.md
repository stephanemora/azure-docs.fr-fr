---
title: Comment utiliser la propriété « Entité attendue » des actions de Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser la propriété « Entité attendue » d’un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 01d991cff9b7f7a66740f86e537833ffe4e862c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707920"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Comment utiliser la propriété « Entité attendue » des actions

Ce didacticiel explique l’utilisation de la propriété « Entité attendue » des actions.

## <a name="video"></a>Vidéo

[![Didacticiel sur l’entité attendue en préversion](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Utilisez la propriété « Entité attendue » d’une action pour enregistrer la réponse de l’utilisateur pour cette action dans une entité.

Lorsque vous ajoutez des entités à la propriété « Entité attendue » d’une action, le système procède comme suit :

1. Il tente de trouver des entités en utilisant le modèle d’extraction d’entité basé sur l’apprentissage automatique.
2. Si aucune entité n’est trouvée, il affecte tout l’énoncé de utilisateur à $entity sur la base d’une heuristique.
3. Appelez `EntityDetectionCallback`, et passez à la sélection d’action.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Créer le modèle

1. Dans l’interface utilisateur web, cliquez sur « New Model » (Nouveau modèle).
2. Dans le champ « Name » (Nom), tapez « ExpectedEntities », puis appuyez sur Entrée.
3. Cliquez sur le bouton « Create » (Créer).

### <a name="entity-creation"></a>Création d’entités

1. Dans le panneau gauche, cliquez sur « Entités », puis sur le bouton « Nouvelle entité ».
2. Pour « Entity Type » (Type d’entité), sélectionnez « Custom Trained » (Formée personnalisée).
3. Tapez « name » pour le « Nom d’entité ».
4. Cliquez sur le bouton « Create » (Créer).

> [!NOTE]
> Le type d’entité « Custom Trained » (Formée personnalisée) signifie que cette entité peut être formée, contrairement à d’autres types d’entités.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Créer la première action

1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
2. Dans le champ « Bot’s response... » (Réponse du bot...), entrez « Quel est votre nom ? ».
3. Dans le champ « Expected Entities » (Entités attendus), tapez le « name ».
4. Cliquez sur le bouton « Create » (Créer).

> [!NOTE]
> Les entités détectées et extraites de la réponse de l’utilisateur seront enregistrées dans l’entité « name » si cette Action est choisie. Si aucune entité n’est détectée, la réponse entière sera enregistrée pour cette entité.

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
2. Dans le champ « Bot’s response... » (Réponse du bot...), entrez « Bonjour $name ! ».
3. Cliquez sur le bouton « Create » (Créer).

> [!NOTE]
> L’entité « name » a été automatiquement ajoutée aux « Required Entities » (Entités requises) par référence dans la réponse.

Vous avez maintenant deux actions.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage), puis sur le bouton « New Train Dialog » (Nouveau dialogue d’apprentissage).
2. Dans le panneau de conversation, sous « Type your message... » (Tapez votre message...), entrez « Bonjour ».
    - Cela simule le côté utilisateur de la conversation.
3. Cliquez sur le bouton « Sore actions » (Noter les actions).
4. Sélectionnez la réponse « Quel est votre nom ? ».
    - Vous ne pouvez pas sélectionner la réponse « Bonjour $name ! » car elle nécessite que l’entité « name » soit définie dans la mémoire du modèle.
5. Dans le panneau de conversation, sous « Type your message... » (Entrez votre message...), tapez « Frank ».
    - « Frank » est mis en surbrillance en tant qu’entité sur la base de l’heuristique que nous avons définie précédemment pour enregistrer la réponse en tant qu’entité.
6. Cliquez sur le bouton « Sore actions » (Noter les actions).
    - L’entité « name » est maintenant définie comme étant « Frank » dans la mémoire du modèle. Par conséquent, l’action « Hello $name » peut être sélectionnée en tant qu’action.
7. Sélectionnez la réponse « Bonjour $name ! »
8. Cliquez sur le bouton « Save » (Enregistrer).

L’ajout d’entrées supplémentaires complète l’apprentissage du modèle.

1. Dans le champ « Add alternative input... » (Ajoutez une autre entrée...), tapez « Je suis Jose ».
    - Le modèle ne reconnaît pas le nom en tant qu’entité. Il sélectionne donc le bloc de texte entier en tant que valeur de l’entité.
2. Cliquez sur la phrase « Je suis Jose », puis cliquez sur l’icône Corbeille.
3. Cliquez sur « Jose », puis sur « name » dans la liste d’entités.
4. Cliquez sur Score Actions (Actions de score).
5. Sélectionnez la réponse « Bonjour Frank ! »
6. Cliquez sur le bouton « Save » (Enregistrer).

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités pouvant être niées](./06-negatable-entities.md)
