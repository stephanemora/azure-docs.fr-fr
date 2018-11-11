---
title: Modèle Conversation Learner de démonstration, commande de pizza – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer un modèle Conversation Learner de démonstration.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e23ff60a0a2ea10ace09130ba115e72b4e1c9ad7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249810"
---
# <a name="demo-pizza-order"></a>Démonstration : commande de pizza
Cette démonstration illustre un bot de commande de pizza. Il prend en charge la commande d’une seule pizza avec cette fonctionnalité :

- reconnaître les ingrédients d’une pizza dans les énoncés utilisateur
- vérifier si les ingrédients d’une pizza sont en stock ou en rupture de stock, et réagir de manière appropriée
- mémoriser les ingrédients d’une pizza à partir d’une commande précédente et proposer une nouvelle commande avec les mêmes ingrédients

## <a name="video"></a>Vidéo

[![Aperçu de démonstration Pizza](https://aka.ms/cl-demo-pizza-preview)](https://aka.ms/blis-demo-pizza)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot de commande de pizza soit en cours d’exécution

    npm run demo-pizza

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles de l’interface utilisateur web, cliquez sur TutorialDemo Pizza Order. 

## <a name="entities"></a>Entités

Vous avez créé trois entités.

- Toppings : cette entité accumule les garnitures que l’utilisateur a demandées. Cela inclut les ingrédients valides en stock. Et vérifie si un ingrédient est en stock ou en rupture de stock.
- OutofStock : cette entité sert à indiquer à l’utilisateur que la garniture sélectionnée est en rupture de stock.
- LastToppings : une fois qu’une commande est passée, cette entité est utilisée pour proposer à l’utilisateur la liste des ingrédients dans sa commande.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Actions

Vous avez créé un ensemble d’actions : demander à l’utilisateur ce qu’il veut sur sa pizza, lui indiquer ce qu’il a ajouté jusqu’ici, etc.

Il y a également deux appels d’API :

- FinalizeOrder : passer la commande de la pizza
- UseLastToppings : migrer les ingrédients de la commande précédente 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogues d’apprentissage
Vous avez défini quelques dialogues d’apprentissage. 

![](../media/tutorial_pizza_dialogs.PNG)

À titre d’exemple, nous allons tester une session d’apprentissage.

1. Cliquez sur Dialogues d’apprentissage, puis sur Nouveau dialogue d’apprentissage.
1. Entrez « order a pizza » (commander une pizza).
2. Cliquez sur Action de score.
3. Cliquez pour sélectionner « what would you like on your pizza » (que voulez-vous sur votre pizza ?)
4. Entrez « mushrooms and cheese » (champignons et fromage).
    - Notez que LUIS a étiqueté les deux en tant qu’ingrédients. Si cela n’est pas correct, vous pouvez cliquer pour les sélectionner, puis les corriger.
    - Le signe « + » en regard de l’entité signifie qu’elle est ajoutée à l’ensemble des ingrédients.
5. Cliquez sur Score Actions (Actions de score).
    - Vous remarquerez que `mushrooms` et `cheese` ne sont pas dans la mémoire des garnitures.
3. Cliquez pour sélectionner « you have $Toppings on your pizza » (vous avez $Toppings sur votre pizza)
    - Notez qu'il s’agit d’une action de non-attente, donc le bot demande l’action suivante.
6. Sélectionnez « Would you like anything else? » (Voulez-vous autre chose ?)
7. Entrez « remove mushrooms and add peppers » (supprimer les champignons et ajouter des poivrons).
    - Vous remarquerez qu’un signe « - » s’affiche à côté de `mushroom` pour pouvoir le supprimer. Par ailleurs, `peppers` est accompagné d’un signe « + » qui permet de l’ajouter aux garnitures.
2. Cliquez sur Score Action (Action de score).
    - Vous remarquerez que `peppers` est maintenant affiché en gras, car il s’agit d’un nouvel élément. Par ailleurs, `mushrooms` a été barré.
8. Cliquez pour sélectionner « you have $Toppings on your pizza » (vous avez $Toppings sur votre pizza)
6. Sélectionnez « Would you like anything else? » (Voulez-vous autre chose ?)
7. Entrez « add peas » (ajouter des petits pois).
    - `Peas` est un exemple de garniture en rupture de stock. Il est toujours indiqué comme étant une garniture.
2. Cliquez sur Score Action (Action de score).
    - `Peas` apparaît comme OutOfStock.
    - Pour voir pourquoi, ouvrez le code sur `C:\<\installedpath>\src\demos\demoPizzaOrder.ts`. Regardez la méthode EntityDetectionCallback. Cette méthode est appelée après chaque ingrédient pour voir si celui-ci est en stock. Si ce n’est pas le cas, l’ingrédient est supprimé de l’ensemble des ingrédients et ajouté à l’entité OutOfStock. La variable inStock est définie au-dessus de cette méthode qui présente la liste des ingrédients en stock.
6. Sélectionnez « We don't have $OutOfStock » (Nous n’avons pas $OutOfStock).
7. Sélectionnez « Would you like anything else? » (Voulez-vous autre chose ?)
8. Entrez « no » (non).
9. Cliquez sur Action de score.
10. Sélectionnez l’appel de l’API « FinalizeOrder ». 
    - La fonction « FinalizeOrder » définie dans le code est appelée. Cela efface les ingrédients et retourne « your order is on its way » (votre commande est en cours). 
2. Entrez « order another » (commander une autre). Nous commençons une nouvelle commande.
9. Cliquez sur Score Action (Action de score).
    - « cheese » et « peppers » sont en mémoire en tant que garnitures de la commande précédente.
1. Sélectionnez « Would you like $LastToppings » (Voulez-vous $LastToppings).
2. Entrez « yes » (oui)
3. Cliquez sur Action de score.
    - Le bot veut exécuter l’action UseLastToppings. C’est la deuxième des deux méthodes de rappel. Cela permet de copier les ingrédients de la dernière commande dans les ingrédients et d’effacer les derniers ingrédients. Il s’agit d’un moyen de mémoriser la dernière commande et, si l’utilisateur indique qu’il veut une autre pizza, de proposer ces ingrédients en tant qu’options.
2. Cliquez pour sélectionner « you have $Toppings on your pizza » (vous avez $Toppings sur votre pizza).
3. Sélectionnez « Would you like anything else? » (Voulez-vous autre chose ?)
8. Entrez « no » (non).
4. Cliquez sur Done Teaching (Apprentissage terminé).

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démonstration : lanceur d’applications VR](./demo-vr-app-launcher.md)
