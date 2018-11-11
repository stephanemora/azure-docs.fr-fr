---
title: Comment utiliser des entités à valeurs multiples avec un modèle d’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des entités à valeurs multiples avec un modèle d’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9ef86ea728e197540180d486dc1cb171f78a2590
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229456"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Utiliser des entités à valeurs multiples avec un modèle d’Apprenant de conversation
Ce tutoriel montre la propriété « valeur multiple » des entités.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 6](https://aka.ms/cl-tutorial-06-preview)](https://aka.ms/blis-tutorial-06)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Une entité « à valeurs multiples » accumule des valeurs dans une liste plutôt que de stocker une valeur unique.  C’est utile pour les entités où l’utilisateur peut spécifier plusieurs valeurs, telles que des garnitures sur une pizza.

Concrètement, si une entité est marquée comme « valeur multiple », chaque instance reconnue de l’entité sera ajoutée à une liste dans la mémoire du bot (plutôt que de remplacer une valeur d’entité unique).

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Création du modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans Nom, entrez MultiValueEntities. Cliquez ensuite sur Créer.

### <a name="create-an-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans Nom de l’entité, entrez Garnitures.
3. Vérifiez les valeurs multiples.
    - Les entités à valeurs multiples accumulent une ou plusieurs valeurs dans l’entité.
2. Vérifiez ce qui peut être nié.  
    - Ceci permettra à l’utilisateur de supprimer des garnitures de sa liste de garnitures de pizza cumulées.
3. Cliquez sur Créer.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle action
2. Dans Réponse, tapez « Quelle garniture voulez-vous ? ».
3. Dans Entités disqualifiantes, entrez Garnitures.
3. Cliquez sur Create.

Ensuite, créez la deuxième action.

1. Cliquez sur Actions, puis sur Nouvelle Action pour en créer une deuxième.
3. En réponse, tapez « Voici vos garnitures : $Toppings ».
4. Cliquez sur Create.

Vous avez maintenant deux actions.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Former le bot

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelles garnitures voulez-vous ? »
2. Entrez « champignons et fromage ». 
    - Vous pouvez étiqueter zéro, un ou plusieurs des entités.
3. Cliquez sur « champignons », puis sélectionnez des garnitures.
4. Cliquez sur « fromage », puis sélectionnez des garnitures.
5. Cliquez sur Attribuer un score aux actions
    - Les deux valeurs sont désormais présentes dans l’entité Garnitures. 
6. Sélectionnez « Voici vos garnitures : $Toppings ».

Nous pouvons également ajouter :

7. Entrez « ajouter des poivrons ».
    - Cliquez sur « poivrons » sous la détection d’entité, puis sélectionnez des ingrédients.
3. Cliquez sur Score Actions (Actions de score).
    - Maintenant, les « poivrons » s’affichent comme valeur supplémentaire dans les garnitures.
6. Sélectionnez « Voici vos garnitures : $Toppings ».

Nous allons supprimer une garniture et en ajouter une :

2. Tapez « supprimer les poivrons et ajouter du salami ».
1. Cliquez sur « poivrons », puis sur le x rouge pour les supprimer.
2. Cliquez sur « poivrons » et sélectionnez « Garnitures ».
3. Cliquez sur Score Actions (Actions de score).
    - « poivrons » a été supprimé et « salami » a été ajouté.
6. Sélectionnez « Voici vos garnitures : $Toppings ».

Maintenant nous allons essayez de tout supprimer :

6. Entrez « supprimer champignons, supprimer fromage et supprimer salami ».
7. Cliquez sur chacun des trois, puis sélectionnez « -Garnitures ».
7. Cliquez sur Score Actions (Actions de score).
    - Toutes les garnitures sont désactivées.
2. Sélectionnez « Quelles garnitures voulez-vous ? »
3. Cliquez sur Apprentissage terminé

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités intégrées](./7-built-in-entities.md)
