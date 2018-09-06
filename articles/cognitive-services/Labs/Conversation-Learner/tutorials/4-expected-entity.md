---
title: Comment utiliser la propriété « Entité attendue » des actions Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser la propriété « entité attendue » d’un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: fb9193066c9501341efb779b9f9e2ccace02cccf
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888202"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Comment utiliser la propriété « Entité attendue » des actions

Ce didacticiel explique l’utilisation du champ « Entité attendue » des actions.

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel 4](http://aka.ms/cl-tutorial-04-preview)](http://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Utilisez le champ « Entité attendue » d’une action pour indiquer au système que vous vous attendez à ce que la réponse de l’utilisateur à une action soit de déclarer une entité.

Concrètement, si le champ « Entité attendue » d’une action est défini sur $entity, alors au prochaine énoncé de l’utilisateur, le système effectuera les opérations suivantes :

1. Tout d’abord, comme d’habitude, il essaiera de rechercher des entités à l’aide du modèle d’extraction d’entités basé sur l’apprentissage machine.
2. Si aucune entité n’est trouvée à l’étape 1, alors--comme heuristique--il attribuera l’énoncé utilisateur entier à $entity.
3. Appelez `EntityDetectionCallback` comme d’habitude, et passez à la sélection d’action.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Création du modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans Nom, entrez ExpectedEntities. Cliquez ensuite sur Créer.

### <a name="create-an-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans Nom de l’entité, entrez le nom.
3. Cliquez sur Create.

> [!NOTE]
> Le type d’entité est « CUSTOM », ce qui signifie que l’entité peut être entraînée.  Il existe également des entités prédéfinies, dont le comportement n’est pas ajustable.  Elles sont traitées dans le [tutoriel Entités prédéfinies](./7-built-in-entities.md).

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle action.
2. Dans Réponse, tapez « Quel est votre nom ? ».
3. Dans Entités attendues, entrez $name. Cliquez sur Enregistrer.
    - Cette valeur signifie que, si cette question est posée et que la réponse de l’utilisateur ne comporte aucune entité détectée, le bot doit supposer que cette entité correspond à la totalité de la réponse.
2. Cliquez sur Actions, puis sur Nouvelle Action pour en créer une deuxième.
3. Dans Réponse, tapez « Hello $name ».
    - L’entité est automatiquement ajoutée en tant qu’entité requise. 
4. Cliquez sur Enregistrer.

Vous avez maintenant deux actions.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Former le bot

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quel est votre nom ? ».
    - La réponse « hello, $name » n’est pas sélectionnable, car l’entité $name doit être définie ; or, elle ne se trouve pas dans la mémoire du bot.
2. Entrez « david ». 
    - Le nom est mis en surbrillance comme étant une entité. Cela est dû à l’heuristique que nous avons définie plus haut pour sélectionner la réponse en tant qu’entité.
5. Cliquez sur Attribuer un score aux actions
    - La valeur du nom figure maintenant dans la mémoire du bot.
    - « Bonjour $name » est désormais disponible en tant que réponse. 
6. Sélectionnez « Bonjour $name ».
7. Cliquez sur Apprentissage terminé.

Voici deux exemples dans lesquels le modèle d’extraction d’entités d’apprentissage machine identifie un nom, et l’heuristique « Entité attendue » n’est pas déclenchée.

1. Cliquez sur Nouveau dialogue d’apprentissage.
2. Entrez « mon nom est david ».
    - Le modèle identifie david comme entité de nom, car il a déjà vu ce mot.
2. Cliquez sur Attribuer un score aux actions
3. Sélectionnez « Bonjour $name ».
4. Entrez « mon nom est susan ».
    - Le modèle identifie susan comme nom, car il a déjà vu ce modèle.
2. Cliquez sur Score Actions (Actions de score).
2. Sélectionnez « Bonjour susan ».
3. Cliquez sur Done Teaching (Apprentissage terminé).

Dans les exemples suivants, l’heuristique « entité attendue » se déclenche, mais elle n’est pas correcte. Les exemples montrent comment effectuer la correction.

1. Tapez « appelez-moi jose ».
    - Le modèle ne reconnaît pas le nom comme entité.
2. Cliquez sur jose, puis sélectionnez le nom.
3. Cliquez sur Attribuer un score aux actions.
4. Sélectionnez bonjour $name.
5. Cliquez sur Apprentissage terminé.
1. Cliquez sur Nouveau dialogue d’apprentissage.
2. Entrez « bonjour ».
3. En réponse à « quel est votre nom », entrez « Je m’appelle frank ».
    - L’expression tout entière est mise en surbrillance. Cela provient du fait que le modèle statistique n’a pas trouvé de nom ; l’heuristique s’est donc déclenchée et a sélectionné la réponse entière comme entité de nom.
2. Pour le corriger, cliquez sur l’expression en surbrillance, puis cliquez sur le x rouge. 
3. Cliquez pour sélectionner frank, puis cliquez sur le nom.
2. Cliquez sur Attribuer un score aux actions.
3. Sélectionnez « Bonjour $name ».
4. Cliquez sur Done Teaching (Apprentissage terminé).

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités pouvant être niées](./5-negatable-entities.md)
