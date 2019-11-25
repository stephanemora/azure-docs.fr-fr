---
title: Étiqueter une entité dans un exemple d’énoncé
titleSuffix: Azure Cognitive Services
description: Découvrez comment étiqueter une entité issue du Machine Learning avec des sous-composants dans un exemple d’énoncé, dans une page de détails d’intention du portail LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134119"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Étiqueter une entité issue du Machine Learning dans un exemple d’énoncé

L’étiquetage d’une entité dans un exemple d’énoncé donne à LUIS un exemple de cette entité et indique où celle-ci peut apparaître dans l’énoncé. 

## <a name="labeling-machine-learned-entity"></a>Étiquetage d’une entité issue du Machine Learning

Prenez la phrase `hi, please I want a cheese pizza in 20 minutes` (Bonjour, je voudrais une pizza au fromage dans 20 minutes). 

1. Sélectionnez le texte le plus à gauche, puis sélectionnez le texte le plus à droite de l’entité. L’option _CompleteOrder_ (Terminer la commande) est étiquetée dans l’image suivante.

    > [!div class="mx-imgBorder"]
    > ![Étiquetage de l’entité CompleteOrder issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Sélectionnez l’entité dans la fenêtre contextuelle. L’entité CompleteOrder comprend tous les mots (de gauche à droite en anglais) qui sont étiquetés. 

> [!TIP]
> Les entités disponibles dans la fenêtre contextuelle sont relatives au contexte dans lequel le texte s’affiche. Par exemple, si vous avez une entité Machine Learning à 5 niveaux et si vous sélectionnez le texte au troisième niveau (indiqué par un nom d’entité étiqueté sous l’exemple d’énoncé), les entités disponibles dans la fenêtre contextuelle seront limitées au contexte des sous-composants du 3e niveau (c’est-à-dire les sous-composants du 4e niveau). 

## <a name="review-labeled-text"></a>Vérifier le texte étiqueté

Après l’étiquetage, passez en revue l’exemple d’énoncé. LUIS applique le modèle actuel à l’exemple d’énoncé après l’étiquetage. La ligne pleine indique que le texte a été étiqueté. 

> [!div class="mx-imgBorder"]
> ![Entité CompleteOrder issue du Machine Learning étiquetée](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Quand commencer l’entraînement ?

Si le modèle actuel doit prendre en charge votre entité étiquetée, et si l’exemple d’énoncé continue d’afficher le texte prédit mais sans étiquette, vous devez entraîner votre application.  

## <a name="confirm-predicted-entity"></a>Vérifier l’entité prédite

Si l’indicateur visuel se trouve au-dessus de l’énoncé, il indique que le texte est prédit, mais qu’il n’est _pas encore étiqueté_. Pour transformer la prédiction en étiquette, sélectionnez l’énoncé, puis sélectionnez **Confirmer les prédictions d’entité**.

> [!div class="mx-imgBorder"]
> ![Prédire l’entité CompleteOrder issue du Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Étiqueter un sous-composant d’entité à l’aide du curseur de la palette des entités

1. Pour corriger les prédictions (entités qui apparaissent au-dessus de l’exemple d’énoncé), ouvrez la palette des entités. 

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Sélectionnez le sous-composant d’entité. Cette action est indiquée visuellement par un nouveau curseur. Le curseur suit les déplacements de la souris dans le portail. 

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. Dans l’exemple d’énoncé, _peignez_ l’entité avec le curseur. 

    > [!div class="mx-imgBorder"]
    > ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Étiquetage des entités de texte correspondant à une entité issue du Machine Learning

Les entités de texte de correspondant incluent des entités prédéfinies, des entités d’expression régulière et des entités de liste. Vous pouvez les ajouter à une entité issue du Machine Learning en tant que contraintes de sous-composant lorsque vous créez ou modifiez l’entité. 

**Une fois ces contraintes ajoutées, vous n’avez pas besoin d’étiqueter le texte correspondant dans l’exemple d’énoncé**.

## <a name="entity-prediction-errors"></a>Erreurs de prédiction des entités

Les erreurs de prédiction des entités affichent un indicateur d’avertissement. Celui-ci indique que l’entité prédite ne correspond pas à l’entité étiquetée. 

> [!div class="mx-imgBorder"]
> ![Palette des entités pour l’entité issue du Machine Learning](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Étapes suivantes

Dans le [tableau de bord](luis-how-to-use-dashboard.md), [passez en revue les énoncés de point de terminaison](luis-how-to-review-endpoint-utterances.md) pour améliorer la qualité de prédiction de votre application.
