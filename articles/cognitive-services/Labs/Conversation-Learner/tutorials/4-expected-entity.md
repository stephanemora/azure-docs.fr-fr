---
title: Comment utiliser la propriété « Entité attendue » des actions Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser la propriété « Entité attendue » d’une application Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369625"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Comment utiliser la propriété « Entité attendue » des actions

Ce didacticiel explique l’utilisation du champ « Entité attendue » des actions.

## <a name="requirements"></a>Configuration requise
Ce didacticiel requiert l’exécution du robot de didacticiel général.

    npm run tutorial-general

## <a name="details"></a>Détails
Utilisez le champ « Entité attendue » d’une action pour indiquer au système que vous vous attendez à ce que la réponse de l’utilisateur à une action soit de déclarer une entité.

Concrètement, si le champ « Entité attendue » d’une action est défini sur $entity, alors au prochaine énoncé de l’utilisateur, le système effectuera les opérations suivantes :

1. Tout d’abord, comme d’habitude, il essaiera de rechercher des entités à l’aide du modèle d’extraction d’entités basé sur l’apprentissage machine.
2. Si aucune entité n’est trouvée à l’étape 1, alors--comme heuristique--il attribuera l’énoncé utilisateur entier à $entity.
3. Appelez `EntityDetectionCallback` comme d’habitude, et passez à la sélection d’action.

## <a name="steps"></a>Étapes

### <a name="create-the-application"></a>Création de l'application

1. Dans l’interface utilisateur web, cliquez sur Nouvelle application.
2. Dans Nom, entrez ExpectedEntities. Cliquez ensuite sur Créer.

### <a name="create-an-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans Nom de l’entité, entrez le nom.
3. Click Create

Notez que le type d’entité est « personnalisé », ce qui signifie que l’entité peut être formée.  Il existe également des entités prédéfinies, ce qui signifie que leur comportement ne peut pas être ajusté. Nous les aborderons dans un autre didacticiel.

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle action.
2. Dans Réponse, tapez « Quel est votre nom ? ».
3. Dans Entités attendues, entrez $name. Cliquez sur Enregistrer.
    - Cela signifie que si cette question est posée et que la réponse de l’utilisateur n’inclut aucune entité détectée, le robot doit supposer que la totalité de la réponse de l’utilisateur est cette entité.
2. Cliquez sur Actions, puis sur Nouvelle Action pour créer une deuxième action.
3. Dans Réponse, tapez « Hello $name ».
    - Notez que l’entité est automatiquement ajoutée en tant qu’entité disqualifiante. 
4. Cliquez sur Enregistrer.

Vous avez maintenant deux actions.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Former le robot

1. Cliquez sur Dialogues d’apprentissage, puis sur Nouveau dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quel est votre nom ? ».
    - Notez que la réponse « Bonjour $name » ne peut pas être sélectionnée car l’entité $name doit être définie, et $name ne se trouve pas dans la mémoire du robot.
2. Entrez « david ». 
    - Notez que le nom est mis en surbrillance comme entité. Cela est dû à l’heuristique que nous avons définie plus haut pour sélectionner la réponse en tant qu’entité.
5. Cliquez sur Attribuer un score aux actions.
    - Veuillez noter que la valeur du nom figure maintenant dans la mémoire du robot.
    - « Bonjour $name » est désormais disponible en tant que réponse. 
6. Sélectionnez « Bonjour $name ».
7. Cliquez sur Apprentissage terminé.

Voici deux exemples dans lesquels le modèle d’extraction d’entités d’apprentissage machine identifie un nom, et l’heuristique « Entité attendue » n’est pas déclenchée.

1. Cliquez sur Nouveau dialogue d’apprentissage.
2. Entrez « mon nom est david ».
    - Notez qu’il identifie david comme entité de nom, car il a déjà vu ce mot auparavant.
2. Cliquez sur Attribuer un score aux actions.
3. Sélectionnez « Bonjour $name ».
4. Entrez « mon nom est susan ».
    - Notez qu’il identifie susan comme nom, car il a déjà vu ce modèle.
2. Cliquez sur Attribuer un score aux actions.
2. Sélectionnez « Bonjour susan ».
3. Cliquez sur Apprentissage terminé.

Voici deux autres exemples dans lesquels l’heuristique « Entité attendue » se déclenche, mais est incorrecte, avec la méthode de correction.

1. Tapez « appelez-moi jose ».
    - Notez qu’il ne reconnaît pas le nom comme entité.
2. Cliquez sur jose, puis sélectionnez le nom.
3. Cliquez sur Attribuer un score aux actions.
4. Sélectionnez bonjour $name.
5. Cliquez sur Apprentissage terminé.
1. Cliquez sur Nouveau dialogue d’apprentissage.
2. Entrez « bonjour ».
3. En réponse à « quel est votre nom », entrez « Je m’appelle frank ».
    - Notez que l’expression entière est mise en surbrillance. Cela provient du fait que le modèle statistique n’a pas trouvé de nom ; l’heuristique s’est donc déclenchée et a sélectionné la réponse entière comme entité de nom.
2. Pour le corriger, cliquez sur l’expression en surbrillance, puis cliquez sur le x rouge. 
3. Cliquez pour sélectionner frank, puis cliquez sur le nom.
2. Cliquez sur Attribuer un score aux actions.
3. Sélectionnez « Bonjour $name ».
4. Cliquez sur Apprentissage terminé.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités pouvant être niées](./5-negatable-entities.md)
