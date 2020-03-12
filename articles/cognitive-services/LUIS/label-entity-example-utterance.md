---
title: Étiqueter une entité dans un exemple d’énoncé
titleSuffix: Azure Cognitive Services
description: Découvrez comment étiqueter une entité issue du Machine Learning avec des sous-composants dans un exemple d’énoncé, dans une page de détails d’intention du portail LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 5499e8362e1107a18b997a6a485e11343c328d0d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898374"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Étiqueter une entité issue du Machine Learning dans un exemple d’énoncé

L’étiquetage d’une entité dans un exemple d’énoncé donne à LUIS un exemple de cette entité et indique où celle-ci peut apparaître dans l’énoncé.

## <a name="labeling-machine-learned-entity"></a>Étiquetage d’une entité issue du Machine Learning

Prenez la phrase `hi, please I want a cheese pizza in 20 minutes` (Bonjour, je voudrais une pizza au fromage dans 20 minutes).

1. Sélectionnez le texte le plus à gauche, puis sélectionnez le texte le plus à droite de l’entité. Ensuite, choisissez l’entité que vous souhaitez étiqueter, en l’occurrence, CompleteOrder. L’option _CompleteOrder_ (Terminer la commande) est étiquetée dans l’image suivante.

    > [!div class="mx-imgBorder"]
    > ![Étiquetage de l’entité CompleteOrder issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Sélectionnez l’entité dans la fenêtre contextuelle. L’entité CompleteOrder comprend tous les mots (de gauche à droite en anglais) qui sont étiquetés.

## <a name="review-labeled-text"></a>Vérifier le texte étiqueté

Après l’étiquetage, vérifiez l’exemple d’énoncé et vérifiez que l’étendue de texte sélectionnée a été soulignée avec l’entité choisie. La ligne pleine indique que le texte a été étiqueté.

> [!div class="mx-imgBorder"]
> ![Entité CompleteOrder issue du Machine Learning étiquetée](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Vérifier l’entité prédite

Si l’étendue de texte est encadrée par des pointillés et si le nom de l’entité se trouve au-dessus de l’énoncé, cela indique que le texte est prédit mais qu’il n’est _pas encore étiqueté_. Pour transformer la prédiction en étiquette, sélectionnez la ligne de l’énoncé, puis sélectionnez **Confirmer les prédictions d’entité**.

> [!div class="mx-imgBorder"]
> ![Prédire l’entité CompleteOrder issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Vous pouvez également sélectionner le nom de l’entité au-dessus du texte, puis sélectionner **Confirmer la prédiction** dans le menu qui s’affiche.

> [!div class="mx-imgBorder"]
> ![Prédire l’entité CompleteOrder issue du Machine Learning avec le menu](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Étiqueter une entité à l’aide du curseur de la palette des entités

La palette des entités offre une alternative à l’expérience d’étiquetage précédente. Elle vous permet de surligner du texte afin de l’étiqueter instantanément avec une entité.

1. Ouvrez la palette des entités en sélectionnant l’icône Surligneur en haut à droite de la table des énoncés.

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Sélectionnez le composant d’entité. Cette action est indiquée visuellement par un nouveau curseur. Le curseur suit les déplacements de la souris dans le portail.

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. Dans l’exemple d’énoncé, _peignez_ l’entité avec le curseur.

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Étiquetage des sous-composants d’une entité issue du Machine Learning

Les sous-composants des entités sont étiquetés exactement de la même façon que les entités de niveau supérieur. Lorsque vous sélectionnez du texte, les entités disponibles dans la fenêtre contextuelle sont relatives au contexte dans lequel le texte s’affiche. Par exemple, si vous avez une entité issue du Machine Learning à 5 niveaux et si vous sélectionnez du texte qui a été étiqueté avec les premier et deuxième niveaux (indiqués par un nom d’entité étiqueté sous l’exemple d’énoncé), les entités disponibles dans la fenêtre contextuelle seront limitées au contexte des sous-composants du troisième niveau. Pour étiqueter le texte avec d’autres entités, sélectionnez l’option **Étiqueter comme une autre entité**.

> [!div class="mx-imgBorder"]
> ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Les sous-composants peuvent être étiquetés uniquement si le parent est également étiqueté.

## <a name="labeling-entity-roles"></a>Étiquetage des rôles d’entité

Les rôles d’entité sont étiquetés à l’aide de la palette d’entités.

1. Dans la page des détails de l’intention, sélectionnez la **palette d’entités** dans la barre d’outils contextuelle.
1. Une fois la palette d’entités ouverte, sélectionnez l’entité dans la liste des entités.
1. Accédez à l’**inspecteur d’entité**, puis sélectionnez un rôle existant ou créez-en un.
1. Dans l’exemple de texte d’énoncé, étiquetez le texte avec le rôle d’entité.

## <a name="unlabel-entities"></a>Supprimer les étiquettes des entités

Pour supprimer l’étiquette d’une entité, sélectionnez le nom de l’entité situé sous le texte et sélectionnez **Unlabel** (Supprimer l’étiquette). Si l’entité dont vous souhaitez supprimer l’étiquette comprend des sous-composants étiquetés, vous devez d’abord supprimer les étiquettes de ces sous-composants.

## <a name="editing-labels-using-the-entity-palette"></a>Modification des étiquettes à l’aide de la palette des entités

Si vous vous trompez lors de l’étiquetage, la palette des entités vous permettra d’apporter des modifications facilement et rapidement. Par exemple, si une étiquette d’entité inclut un mot en trop et si les sous-composants ont déjà été étiquetés, vous pouvez utiliser la palette des entités pour surligner uniquement les mots nécessaires.

Par exemple :

1. Le sous-composant PizzaType (Type de pizza) inclut les mots « cheese pizza with » (pizza au fromage avec). Ici, le mot « with » ne devrait pas être inclus.

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/edit-label-with-palette-1.png)

2. Utilisez la palette des entités pour sélectionner PizzaType et surligner « cheese pizza » (pizza au fromage). De cette façon, seuls les mots « cheese pizza » sont associés à l’étiquette « PizzaType ».

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Étiquettes des entités avec correspondance de texte

Les entités avec correspondance de texte incluent les entités prédéfinies, les entités d’expression régulière, les entités de liste et les entités pattern.any. Celles-ci sont automatiquement étiquetées par LUIS. Vous n’avez donc pas à le faire manuellement.

## <a name="entity-prediction-errors"></a>Erreurs de prédiction des entités

Les erreurs de prédiction d’entité indiquent que l’entité prédite ne correspond pas à l’entité étiquetée. Ces erreurs sont signalées par la présence d’un indicateur d’avertissement en regard de l’énoncé.

> [!div class="mx-imgBorder"]
> ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Étapes suivantes

Dans le [tableau de bord](luis-how-to-use-dashboard.md), [passez en revue les énoncés de point de terminaison](luis-how-to-review-endpoint-utterances.md) pour améliorer la qualité de prédiction de votre application.