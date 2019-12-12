---
title: 'Didacticiel : Données contextuelles avec des rôles - LUIS'
titleSuffix: Azure Cognitive Services
description: Recherchez des données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination d’un déménagement physique depuis un bâtiment et un bureau vers un autre bâtiment et un autre bureau sont liés.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 29e43692c1eb543768934a961a2bb8ae5a023b1d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894603"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Didacticiel : Extraire des données connexes en fonction du contexte d’un énoncé

Dans ce tutoriel, vous allez rechercher des ensembles de données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination pour un transfert entre une ville à une autre. Les deux ensembles de données peuvent être nécessaires et ils sont liés entre eux.

Un rôle peut être utilisé avec un type d’entité prédéfini ou personnalisé, à la fois dans les exemple d’énoncés et de modèles.

[!INCLUDE [Only valid with current portal](includes/old-portal-only.md)]

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer une application
> * Ajouter une intention
> * Obtenir des informations d’origine et de destination à l’aide de rôles
> * Former
> * Publish
> * Obtenir des intentions et des rôles d’entité à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Données associées

Cette application détermine les villes d’origine et de destination entre lesquelles un employé doit être déplacé. Elle utilise une entité prédéfinie GeographyV2 pour identifier les noms des villes et des rôles pour déterminer les types de localisation (origine et destination) dans l’énoncé.

Un rôle doit être utilisé quand les données d’entité à extraire :

* Sont liées entre elles dans le contexte de l’énoncé.
* Utilisent des formulations spécifiques pour indiquer chaque rôle. Exemples : de/à, quitte/arrive à, loin de/vers...
* Les deux rôles sont souvent dans le même énoncé, ce qui permet à LUIS d’apprendre de cette utilisation contextuelle fréquente.
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.

## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Créer une intention de déplacer des employés entre des villes

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Sélectionnez **Créer une intention**.

1. Entrez `MoveEmployeeToCity` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

    ![Capture d’écran de la boîte de dialogue contextuelle Créer une nouvelle intention](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Déplacer John W. Smith de Seattle à Orlando|
    |Transférer Jill Jones de Seattle vers Le Caire|
    |Éloigner John Jackson de Tampa, destination Atlanta |
    |Déplacer Debra Doughtery de Tulsa vers Chicago|
    |Dép Jill Jones du Caire vers Tampa|
    |Muter Alice Anderson de Paris à Redmond|
    |Carl Chamerlin de San Francisco à Redmond|
    |Transférer Steve Standish de San Diego vers Bellevue |
    |Retirer Tanner Thompson de Kansas City et l’envoyer à Chicago|

    [![Capture d’écran de LUIS avec les nouveaux énoncés dans l’intention MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Ajouter l’entité prédéfinie geographyV2

L’entité prédéfinie, geographyV2, extrait des informations de localisation, notamment des noms de ville. Dans la mesure où les énoncés comportent deux noms de ville, liés entre eux dans le contexte, utilisez des rôles pour extraire ce contexte.

1. Dans le panneau de navigation côté gauche, sélectionnez **Entités**.

1. Sélectionnez **Ajouter une entité prédéfinie**, puis `geo` dans la barre de recherche pour filtrer les entités prédéfinies.

    ![Ajouter l’entité prédéfinie geographyV2 à l’application](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Cochez la case et sélectionnez **Terminé**.
1. Dans la liste **Entités**, sélectionnez **geographyV2** pour ouvrir la nouvelle entité.
1. Ajoutez deux rôles, `Origin` et `Destination`.

    ![Ajouter des rôles à des entités prédéfinies](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Sélectionnez **Intentions** dans le panneau de navigation côté gauche, puis sélectionnez l’intention **MoveEmployeeToCity**. Notez que les noms des villes sont étiquetés avec l’entité prédéfinie **geographyV2**.
1. Dans le premier énoncé de la liste, sélectionnez la localisation d’origine. Un menu déroulant s’affiche. Sélectionnez **geographyV2** dans la liste, puis parcourez le menu pour sélectionner **Origine**.
1. Utilisez la méthode de l’étape précédente pour marquer tous les rôles des localisations dans tous les énoncés.


## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None »

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les modifications apportées à l’intention puissent être testées

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publier l’application pour que le modèle entraîné soit interrogeable à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Allez à la fin de l’URL dans la barre d’adresses, puis entrez `Please move Carl Chamerlin from Tampa to Portland`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `MoveEmployee` avec l’entité extraite.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    L’intention correcte est prédite et le tableau d’entités présente les rôles d’origine et de destination dans la propriété **entities** correspondante.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* [Concepts d’entités](luis-concept-entity-types.md)
* [Concepts de rôles](luis-concept-roles.md)
* [Liste des entités prédéfinies](luis-reference-prebuilt-entities.md)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)
* [Rôles](luis-concept-roles.md)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une intention et ajouté des exemples d’énoncés pour les données des emplacements d’origine et de destination issues d’un apprentissage contextuel. Une fois l’application entraînée et publiée, une application cliente peut utiliser ces informations pour créer un ticket de déménagement avec les informations appropriées.

> [!div class="nextstepaction"]
> [Découvrez comment ajouter une entité composite](luis-tutorial-composite-entity.md)
