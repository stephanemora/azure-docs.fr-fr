---
title: Ajouter des intentions dans les applications LUIS
titleSuffix: Azure Cognitive Services
description: Ajoutez des intentions à votre application LUIS pour identifier des groupes de questions ou de commandes qui ont les mêmes intentions.
services: cognitive-services
author: diberry
manager: cgronlun
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 9b7207a16a89e48ad64b2dbc48a5293d0cf8aa25
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036080"
---
# <a name="manage-intents"></a>Gérer les intentions 
Ajoutez des [intentions](luis-concept-intent.md) à votre application LUIS pour identifier des groupes de questions ou des commandes qui ont les mêmes intentions. 

La gestion des intentions s’effectue à partir de la section **Build (Générer)** dans la barre d’outils supérieure. Vous ajoutez et gérez vos intentions à partir de la page **Intents (Intentions)**, disponible dans le volet gauche. 

La procédure suivante montre comment ajouter l’intention « Bookflight » dans l’application TravelAgent.

## <a name="add-intent"></a>Ajouter une intention

1. Ouvrez votre application (par exemple, TravelAgent) en cliquant sur son nom sur la page **Mes applications**, puis cliquez sur **Intentions** dans le panneau gauche. 
2. Sur la page **Intentions**, sélectionnez **Create new intent** (Créer une intention).

3. Dans la boîte de dialogue **Create new intent** (Créer une intention), saisissez le nom de l’intention « BookFlight » et cliquez sur **Terminé**.

    ![Ajouter une intention](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Sur la page de détails de l’intention qui vient d’être ajoutée, [ajoutez des énoncés](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Renommer l’intention

1. Sur la page **Intent** (Intention), cliquez sur l’icône Renommer ![Rename Intent](./media/luis-how-to-add-intents/Rename-Intent-btn.png) (Renommer l’intention) en regard du nom de l’intention. 

2. Sur la page **Intent** (Intention), le nom de l’intention actuelle est affiché dans une boîte de dialogue. Modifiez le nom de l’intention et appuyez sur Entrée. Le nouveau nom est enregistré et affiché sur la page de l’intention.

    ![Modifier l’intention](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Supprimer l’intention
Lorsque vous supprimez une intention autre que l’intention None, vous pouvez choisir d’ajouter tous les énoncés à l’intention None. Cela est utile si vous devez déplacer les énoncés au lieu de les supprimer.   

1. Sur la page **Intent** (Intention), cliquez sur le bouton  **Delete Intent** (Supprimer l’intention) à droite du nom de l’intention. 

    ![Bouton Delete Intent (Supprimer l’intention)](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Cliquez sur le bouton « OK » dans la boîte de dialogue de confirmation.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Ajouter un énoncé sur la page de l’intention

Sur la page de l’intention, entrez un énoncé pertinent que vous attendez de vos utilisateurs, tel que `book 2 adult business tickets to Paris tomorrow on Air France`, dans la zone de texte sous le nom d’intention, puis appuyez sur Entrée. 
 
>[!NOTE]
>LUIS convertit tous les énoncés en minuscules.

![Capture d’écran de la page de détails Intents (Intentions), avec l’énoncé en surbrillance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Les énoncés sont ajoutés à la liste d’énoncés pour l’intention en cours. Lorsqu’un énoncé est ajouté, [étiquetez les entités](luis-how-to-add-example-utterances.md) dans les énoncés et [formez](luis-how-to-train.md) votre application. 

## <a name="create-a-pattern-from-an-utterance"></a>Créer un modèle à partir d’un énoncé
Voir [Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Modifier un énoncé sur la page de l’intention

Pour modifier un énoncé, sélectionnez le bouton à points de suspension (***...***) à l’extrémité droite de la ligne de cet énoncé, puis sélectionnez **Modifier**. Modifiez le texte, puis appuyez sur la touche Entrée du clavier.

![Capture d’écran de la page de détails Intents (Intentions), avec le bouton à points de suspension en surbrillance](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Réaffecter des énoncés sur la page de l’intention
Vous pouvez modifier l’intention d’un ou plusieurs énoncés en les réaffectant à une autre intention. 

Pour réaffecter un énoncé à une autre intention, à l’extrémité droite de la ligne de l’énoncé, sélectionnez le nom de l’intention appropriée dans la colonne **Labeled intent** (Intention étiquetée). L’énoncé est supprimé de la liste d’énoncés de l’intention actuelle. 

![Capture d’écran de la page de l’intention BookFlight avec une intention d’énoncé dans la colonne Labeled intent (Intention étiquetée) sélectionnée](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Pour modifier l’intention de plusieurs énoncés, cochez les cases à gauche des énoncés, puis sélectionnez **Reassign intent** (Réaffecter l’intention). Sélectionnez l’intention appropriée dans la liste.

![Capture d’écran de la page de l’intention BookFlight avec un énoncé coché et le bouton Reassign intent (Réaffecter l’intention) mis en surbrillance](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Supprimer des énoncés sur la page de l’intention

Pour supprimer un énoncé, sélectionnez le bouton à points de suspension (***...***) à l’extrémité droite de la ligne de cet énoncé, puis sélectionnez **Supprimer**. L’énoncé est supprimé de la liste et de l’application LUIS.

![Capture d’écran de la page de détails Intents (Intentions), avec l’énoncé Supprimer en surbrillance](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Pour supprimer plusieurs énoncés :

1. Cochez les cases à cocher à gauche des énoncés, puis sélectionnez **Delete utterances(s)** (Supprimer le(s) énoncé(s)). 

    ![Capture d’écran de la page de détails Intents (Intentions), avec des énoncés cochés et le bouton Delete utterances(s) (Supprimer le(s) énoncé(s)) mis en surbrillance](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Sélectionnez **Terminé** dans la boîte de dialogue contextuelle **Delete utterances?** (Supprimer les énoncés ?).

## <a name="search-in-utterances-on-intent-page"></a>Rechercher des énoncés sur la page de l’intention
Vous pouvez rechercher des énoncés qui contiennent du texte (mots ou expressions) dans la liste d’énoncés de l’intention. Par exemple, vous pouvez constater une erreur qui implique un mot particulier, et vous souhaitez rechercher tous les exemples qui contiennent ce mot particulier. 

1. Sélectionnez l’icône de la loupe dans la barre d’outils.

    ![Capture d’écran de la page Intents (Intentions), avec l’icône de recherche de la loupe mise en surbrillance](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Une zone de texte de recherche s’affiche. Saisissez le mot ou l’expression dans la zone de recherche dans le coin supérieur droit de la liste des énoncés. Les liste des énoncés est mise à jour pour n’afficher que les énoncés incluant votre texte de recherche. 

    ![Capture d’écran de la page Intents (Intentions), avec la zone de texte de recherche mise en surbrillance](./media/luis-how-to-add-intents/search-textbox.png)

    Pour annuler la recherche et restaurer votre liste complète d’énoncés, supprimez le texte de recherche que vous avez saisi. Pour fermer la zone de texte de recherche, sélectionnez à nouveau l’icône de la loupe dans la barre d’outils.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Erreurs de différence de prédiction sur la page d’une intention
Un énoncé dans une intention peut présenter une différence entre l’intention sélectionnée et le score de prédiction. LUIS indique cette différence avec un encadré rouge autour du score. 

![Capture d’écran de la page de l’intention BookFlight, avec le score de différence de prédiction mis en surbrillance](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrer par erreurs de différence de prédiction sur la page de l’intention
Pour filtrer la liste des énoncés sur les énoncés avec une différence de prédiction d’intention, basculez entre **Afficher tout** et **Errors only** (Erreurs uniquement) dans la barre d’outils. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtrer par type d’entité sur la page de l’intention
Utilisez la liste déroulante **Entity filters** (Filtres d’entité) dans la barre d’outils pour filtrer les énoncés par entité. 

![Capture d’écran de la page Intents (Intentions), avec filtre de type entité mis en surbrillance](./media/luis-how-to-add-intents/filter-by-entities.png) 

Pour supprimer le filtre, sélectionnez la zone de filtre bleue avec ce mot ou cette expression sous la barre d’outils.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Basculer vers la vue du jeton sur la page de l’intention
Activez **Tokens View** (Vue des jetons) pour afficher les jetons au lieu des noms de type d’entité. Sur le clavier, vous pouvez également utiliser **Contrôle + E** pour activer la vue. 

![Capture d’écran de l’intention BookFlight avec la vue du jeton mise en surbrillance](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Former votre application après modification du modèle avec des intentions
Après avoir ajouté, modifié ou supprimé des intentions, [formez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez ajouté des intentions à votre application, la tâche suivante consiste à ajouter des [exemples d’énoncés](luis-how-to-add-example-utterances.md) pour les intentions que vous avez ajoutées. 
