---
title: Ajout de précision à l’aide de modèles
titleSuffix: Language Understanding - Azure Cognitive Services
description: Découvrez comment ajouter des gabarits de modèle dans les applications Language Understanding (LUIS) pour améliorer la précision de la prédiction.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 9d3352017723f5beac318d461a537820b6593bef
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881670"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Comment ajouter des modèles pour améliorer la précision de la prédiction
Lorsqu’une application LUIS reçoit des énoncés de point de terminaison, utilisez le [concept](luis-concept-patterns.md) des modèles pour améliorer la précision de la prédiction des énoncés qui s’appuient sur un modèle d’ordre des mots et de choix de mot. Les modèles utilisent des [entités](luis-concept-entity-types.md) et leurs rôles pour extraire des données à l’aide de la syntaxe spécifique du modèle. 

## <a name="add-template-utterance-to-create-pattern"></a>Ajouter un énoncé de gabarit pour créer un modèle
1. Ouvrez votre application en sélectionnant son nom sur la page **Mes applications**, puis sélectionnez **Patterns** (Modèles) dans le panneau gauche, sous **Improve app performance** (Améliorer les performances de l’application).

    ![Capture d’écran de la liste de modèles](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Sélectionnez l’intention appropriée pour le modèle. 

    ![Sélectionner l’intention](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Dans la zone de texte du gabarit, saisissez l’énoncé du gabarit, puis sélectionnez Entrée. Lorsque vous souhaitez entrer le nom de l’entité, utilisez la syntaxe d’entité de modèle appropriée. Commencez la syntaxe de l’entité avec `{`. La liste des entités s’affiche. Sélectionnez l’entité appropriée, puis Entrée. 

    ![Capture d’écran de l’entité pour le modèle](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Si votre entité inclut un rôle, indiquez le rôle avec un signe deux-points, `:`, après le nom de l’entité, tel que `{Location:Origin}`. La liste des rôles pour les entités s’affiche dans une liste. Sélectionnez le rôle, puis Entrée. 

    ![Capture d’écran de l’entité avec le rôle](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Lorsque vous avez sélectionné l’entité appropriée, terminez d’entrer le modèle, puis sélectionnez Entrée. Lorsque vous avez terminé d’entrer des modèles, [formez](luis-how-to-train.md) votre application.

    ![Capture d’écran du modèle d’entrée avec les deux types d’entités](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Rechercher des modèles
La recherche vous permet de retrouver des modèles qui contiennent du texte donné.  

1. Sélectionnez l’icône de la loupe.

    ![Capture d’écran de la page de modèles avec l’icône de l’outil de recherche mise en surbrillance](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Saisissez le texte à rechercher dans la zone de recherche dans le coin supérieur droit de la liste des modèles, puis sélectionnez Entrée. La liste des modèles est mise à jour pour n’afficher que les modèles contenant le texte recherché.

    ![Capture d’écran de la page de modèles avec le texte recherché dans la zone de recherche mis en surbrillance](./media/luis-how-to-model-intent-pattern/search-text.png)

    Pour annuler la recherche et restaurer votre liste complète de modèles, supprimez le texte de recherche que vous avez saisi.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Modifier un modèle
1. Pour modifier un modèle, sélectionnez le bouton Points de suspension (***...***) à l’extrémité droite de la ligne de ce modèle, puis sélectionnez **Modifier**. 

    ![Capture d’écran de l’élément de menu Modifier dans une ligne de modèle](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Entrez les modifications dans la zone de texte. Lorsque vous avez terminé, sélectionnez Entrée. Lorsque vous avez terminé de modifier des modèles, [formez](luis-how-to-train.md) votre application.

    ![Capture d’écran de modification de modèle](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Réaffecter un modèle à une autre intention

Pour réaffecter un modèle à une autre intention, sélectionnez la zone de liste d’intentions à droite du texte du texte du modèle, puis sélectionnez une autre intention.

![Capture d’écran de réaffectation d’un modèle à une autre intention](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Réaffecter plusieurs modèles à une autre intention

Pour réaffecter plusieurs modèles à une autre intention, cochez la case à gauche de chaque modèle ou cochez la case située en haut. L’option **Reassign intent** (Réaffecter l’intention) s’affiche dans la barre d’outils. Sélectionnez l’intention appropriée pour les modèles. 

![Capture d’écran de réaffectation de plusieurs modèles à une autre intention](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Supprimer un modèle

1. Pour spprimer un modèle, sélectionnez le bouton Points de suspension (***...***) à l’extrémité droite de la ligne de ce modèle, puis sélectionnez **Supprimer**. 

    ![Capture d’écran de suppression d’énoncé](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Sélectionnez **OK** pour confirmer la suppression.

    ![Capture d’écran de confirmation de la suppression](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Supprimer plusieurs modèles

1. Pour supprimer plusieurs modèles, cochez la case à gauche de chaque modèle ou cochez la case située en haut. L’option **Delete patterns(s)** (Supprimer le(s) modèle(s)) s’affiche dans la barre d’outils. Sélectionnez **Delete patterns(s)** (Supprimer le(s) modèle(s)).  

    ![Capture d’écran de suppression de plusieurs modèles](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. La boîte de dialogue de confirmation **Delete patterns** (Supprimer les modèles) s’affiche. Sélectionnez **OK** pour terminer la suppression.

    ![Capture d’écran de la boîte de confirmation de suppression de plusieurs modèles](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Filtrer la liste des modèles par entité

Pour filtrer la liste des modèles sur une entité spécifique, sélectionnez **Entity filters** (Filtres d’entité) dans la barre d’outils située au-dessus des modèles. 

![Capture d’écran de filtrage des modèles par entité](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Une fois que le filtre est appliqué, le nom de l’entité apparaît sous la barre d’outils. 

## <a name="filter-pattern-list-by-intent"></a>Filtrer la liste des modèles par intention

Pour filtrer la liste des modèles sur une entité spécifique, sélectionnez **Intent filters** (Filtres d’intention) dans la barre d’outils située au-dessus des modèles. 

![Capture d’écran de filtrage des modèles par intention](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

Une fois que le filtre est appliqué, le nom de l’intention apparaît sous la barre d’outils. 

## <a name="remove-entity-or-intent-filter"></a>Supprimer un filtre d’entité ou d’intention
Lorsque la liste des modèles est filtrée, le nom de l’entité ou de l’intention apparaît sous la barre d’outils. Pour supprimer le filtre, sélectionnez le nom.

![Capture d’écran de la suppression d’entité dans un filtre](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Le filtre est supprimé et tous les modèles s’affichent. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité
Vous pouvez créer un modèle à partir d’un énoncé existant sur la page **Intention** ou **Entité**. Tous les énoncés sur n’importe quelle page d’intention ou d’entité sont affichés dans une liste avec la colonne de droite permettant d’accéder aux options du niveau de l’énoncé telles que **Modifier**, **Supprimer**, et **Add as pattern** (Ajouter en tant que modèle).

1. Sur la ligne sélectionnée de l’énoncé, sélectionnez le bouton Points de suspension (***...***) à droite de l’énoncé et sélectionnez **Add as pattern** (Ajouter en tant que modèle).

    [![Capture d’écran de la table d’énoncés avec Ajouter un modèle mis en surbrillance dans le menu Options](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Capture d’écran de la table d’énoncés avec Ajouter un modèle mis en surbrillance dans le menu Options")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Modifiez le modèle conformément aux [règles de syntaxe](luis-concept-patterns.md#pattern-syntax). Si l’énoncé que vous avez sélectionné est étiqueté avec des entités, ces entités sont déjà dans le modèle avec la syntaxe appropriée.

    ![Capture d’écran de modèles filtrés par entité](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Former votre application après modification du modèle avec des modèles
Après avoir ajouté, modifié, supprimé ou réaffecté un énoncé, [formez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [générer un modèle](luis-tutorial-pattern.md) avec un modèle et des rôles.
* Découvrez comment [former](luis-how-to-train.md) votre application.
