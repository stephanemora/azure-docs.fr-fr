---
title: 'Tutoriel : Données contextuelles avec des rôles - LUIS'
description: Recherchez des données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination d’un déménagement physique depuis un bâtiment et un bureau vers un autre bâtiment et un autre bureau sont liés.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475824"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Tutoriel : Extraire des données connexes en fonction du contexte d’un énoncé

Dans ce tutoriel, vous allez rechercher des ensembles de données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination pour un transfert d’une ville à une autre. Les deux ensembles de données peuvent être nécessaires et ils sont liés entre eux.

Un rôle peut être utilisé avec un type d’entité prédéfini ou personnalisé, à la fois dans les exemple d’énoncés et de modèles.

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

1. Connectez-vous au [portail LUIS en **préversion**](https://preview.luis.ai).

1. Sélectionnez **+ Nouvelle application de conversation**, entrez le nom `HumanResources`, puis conservez la culture par défaut, **Anglais**. Laissez la description et la ressource de prédiction vides. Sélectionnez **Terminé**.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Créer une intention de déplacer des employés entre des villes

Une intention sert à classifier les énoncés utilisateur en fonction de l’intention de l’utilisateur, déterminée à partir du texte en langage naturel.

Pour classifier un énoncé, l’intention a besoin d’exemples d’énoncés utilisateur qui doivent être classifiés avec cette intention.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Sélectionnez **+ Créer**.

1. Entrez `MoveEmployeeToCity` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la boîte de dialogue contextuelle Créer une intention](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Ajoutez plusieurs exemples d’énoncés à cette intention qui, selon vous, sont susceptibles d’être demandés par un utilisateur.

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

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de LUIS avec les nouveaux énoncés dans l’intention MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Ajouter l’entité prédéfinie geographyV2

L’entité prédéfinie, **geographyV2**, extrait des informations de localisation, notamment des noms de ville. Dans la mesure où les énoncés comportent deux noms de ville, liés entre eux dans le contexte, utilisez des rôles pour extraire ce contexte.

1. Dans le panneau de navigation côté gauche, sélectionnez **Entités**.

1. Sélectionnez **+ Ajouter une entité prédéfinie**, puis entrez `geo` dans la barre de recherche pour filtrer les entités prédéfinies.

    > [!div class="mx-imgBorder"]
    > ![Ajouter l’entité prédéfinie geographyV2 à l’application](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Cochez la case et sélectionnez **Terminé**.

## <a name="add-roles-to-prebuilt-entity"></a>Ajouter des rôles à des entités prédéfinies

1. Dans la liste **Entités**, sélectionnez **geographyV2** pour ouvrir la nouvelle entité.
1. Pour ajouter un rôle, sélectionnez **+** et ajoutez les deux rôles suivants : `Origin` et `Destination`.

    > [!div class="mx-imgBorder"]
    > ![Ajouter des rôles à l’entité prédéfinie](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Étiqueter des rôles d’entité dans les exemples d’énoncés

1. Sélectionnez **Intentions** dans le panneau de navigation côté gauche, puis sélectionnez l’intention **MoveEmployeeToCity**. Notez que les noms des villes sont étiquetés avec l’entité prédéfinie **geographyV2**.
1. Dans la barre d’outils contextuelle, sélectionnez la **palette d’entités** représentée par l’_icône de crayon_.

    > [!div class="mx-imgBorder"]
    > ![Sélectionner la palette d’entités dans la barre d’outils de contenu](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Sélectionnez l’entité prédéfinie, **geographyV2**, puis l’**inspecteur d’entité**.
1. Dans l’**inspecteur d’entité**, sélectionnez un rôle, **Destination**. Cela modifie le curseur de la souris. Servez-vous du curseur pour étiqueter le texte dans tous les énoncés correspondant à l’emplacement de destination.

    > [!div class="mx-imgBorder"]
    > ![Sélectionner un rôle dans la palette d’entités](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Revenez à l’**inspecteur d’entité**, puis changez de rôle en choisissant **Origine**. Servez-vous du curseur pour étiqueter le texte dans tous les énoncés correspondant à l’emplacement d’origine.

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None »

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les changements apportés à l’intention puissent être testés

Pour effectuer l’apprentissage de l’application, sélectionnez **Former**. La formation applique les modifications, telles que les nouvelles entités et les énoncés étiquetés, au modèle actif.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publier l’application pour y accéder à partir du point de terminaison HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Allez à la fin de l’URL dans la barre d’adresses, puis remplacez _YOUR_QUERY_HERE_ par `Please move Carl Chamerlin from Tampa to Portland`.

Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `MoveEmployee` avec l’entité extraite.

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

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

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
