---
title: Précision de l’ajout de modèles - LUIS
titleSuffix: Azure Cognitive Services
description: Ajouter des gabarits de modèle pour améliorer la précision de la prédiction dans les applications Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: eb3e473535c394818772ac949808023254087555
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448055"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Comment ajouter des modèles pour améliorer la précision de la prédiction
Lorsqu’une application LUIS reçoit des énoncés de point de terminaison, utilisez un [modèle](luis-concept-patterns.md) pour améliorer la précision de la prédiction des énoncés qui s’appuient sur un modèle d’ordre des mots et de choix de mot. Les modèles utilisent une [syntaxe](luis-concept-patterns.md#pattern-syntax) spécifique pour indiquer l’emplacement des éléments suivants : [entités](luis-concept-entity-types.md), [rôles](luis-concept-roles.md) d’entités et texte facultatif.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Les modèles incluent uniquement les parents d’entité issus de l’apprentissage automatique, pas les sous-composants.

## <a name="adding-example-utterances-as-pattern"></a>Ajout d’exemples d’énoncés en tant que modèles

Si vous souhaitez ajouter un modèle pour une entité, la méthode _la plus facile_ consiste à créer le modèle à partir de la page Détails de l’intention. Cela garantit que votre syntaxe correspond à l’exemple d’énoncé.

1. Dans le [portail LUIS en préversion](https://preview.luis.ai), sélectionnez l’application dans la page **Mes applications**.
1. Dans la page de la liste **Intentions**, sélectionnez le nom d’intention de l’exemple de énoncé à partir duquel vous souhaitez créer un modèle d’énoncé.
1. Dans la page Détails de l’intention, sélectionnez la ligne de l’exemple d’énoncé que vous souhaitez utiliser comme modèle d’énoncé, puis sélectionnez **+ Ajouter en tant que modèle** dans la barre d’outils contextuelle.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la sélection d’un exemple d’énoncé comme modèle dans la page Détails de l’intention.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Dans la zone contextuelle, sélectionnez **Terminé** dans la page **Confirmer les modèles**. Vous n’avez pas besoin de définir les sous-composants, les contraintes ou les descripteurs des entités. Il vous suffit d’afficher l’entité apprise par l’ordinateur.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la confirmation de l’exemple d’énoncé en tant que modèle dans la page Détails de l’intention.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Si vous avez besoin de modifier le modèle, par exemple en sélectionnant un texte facultatif avec les crochets `[]`, vous devez effectuer cette modification à partir de la page **Modèles**.

1. Dans la barre de navigation, sélectionnez **Effectuer l’apprentissage** pour effectuer l’apprentissage de l’application avec le nouveau modèle.

## <a name="add-template-utterance-using-correct-syntax"></a>Ajouter un modèle d’énoncé de modèle utilisant une syntaxe correcte

1. Ouvrez votre application en sélectionnant son nom sur la page **Mes applications**, puis sélectionnez **Patterns** (Modèles) dans le panneau gauche, sous **Improve app performance** (Améliorer les performances de l’application).

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la liste de modèles](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Sélectionnez l’intention appropriée pour le modèle.

1. Dans la zone de texte du gabarit, saisissez l’énoncé du gabarit, puis sélectionnez Entrée. Lorsque vous souhaitez entrer le nom de l’entité, utilisez la syntaxe d’entité de modèle appropriée. Commencez la syntaxe de l’entité avec `{`. La liste des entités s’affiche. Sélectionnez la bonne entité.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’entité pour le modèle](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Si votre entité inclut un [rôle](luis-concept-roles.md), indiquez le rôle avec un signe deux-points, `:`, après le nom de l’entité, tel que `{Location:Origin}`. La liste des rôles pour les entités s’affiche dans une liste. Sélectionnez le rôle, puis Entrée.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’entité avec le rôle](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Lorsque vous avez sélectionné l’entité appropriée, terminez d’entrer le modèle, puis sélectionnez Entrée. Lorsque vous avez terminé d’entrer des modèles, [formez](luis-how-to-train.md) votre application.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du modèle d’entrée avec les deux types d’entités](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Former votre application après modification du modèle avec des modèles
Après avoir ajouté, modifié, supprimé ou réaffecté un énoncé, [formez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Utiliser la barre d’outils contextuelle

La barre d’outils contextuelle au-dessus de la liste des modèles vous permet d’effectuer les opérations suivantes :

* Rechercher des modèles
* Modifier un modèle
* Réaffecter un modèle à une autre intention
* Réaffecter plusieurs modèles à une autre intention
* Delete-a-single-pattern
* Supprimer plusieurs modèles
* Filtrer la liste des modèles par entité
* Filter-pattern-list-by-intent
* Supprimer un filtre d’entité ou d’intention
* Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un modèle](luis-tutorial-pattern.md) avec pattern.any et des rôles à l’aide d’un tutoriel.
* Découvrez comment [former](luis-how-to-train.md) votre application.
