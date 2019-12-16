---
title: 'Didacticiel : Entité de liste - LUIS'
titleSuffix: Azure Cognitive Services
description: Obtenez des données qui correspondent à une liste d’éléments prédéfinie. Chaque élément de la liste peut avoir des synonymes qui correspondent également exactement
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 83644ac7144adaa664b13b6e7d7993c50da0e779
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852468"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Didacticiel : Obtenir des données exactes correspondant au texte d’un énoncé avec une entité de liste

Ce tutoriel explique comment obtenir des données correspondant exactement à une liste d’éléments prédéfinie.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer l’application et utiliser l’intention existante
> * Ajouter une entité de liste
> * Entraîner, publier et interroger une application pour obtenir des données extraites

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Qu’est-ce qu’une entité de liste ?

Une entité de liste est une correspondance de texte exacte par rapport aux mots de l’énoncé. Chaque élément de la liste peut inclure une liste de synonymes. Utilisez une entité de liste quand vous voulez une correspondance exacte.

Pour cette application de commande de pizzas importée, créez une entité de liste pour les différents types de croûte de pizza.

Une entité de type liste est un choix approprié pour ce type de données lorsque :

* Les valeurs des données sont un ensemble connu.
* L’ensemble ne dépasse pas les [limites](luis-boundaries.md) maximum de LUIS pour ce type d’entité.
* Le texte de l’énoncé est une correspondance exacte avec un synonyme ou le nom canonique. LUIS n’utilise pas la liste au-delà des correspondances de texte exactes. Une simple entité de liste ne suffit pas pour résoudre la recherche de radical, les pluriels et autres variantes. Pour gérer les variantes, envisagez d’utiliser un [modèle](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) avec la syntaxe de texte facultative.

> [!CAUTION]
> Si vous n’êtes pas sûr de vouloir une entité de liste ou une entité apprise par la machine avec une liste d’expressions comme descripteur, la pratique la plus efficace et la plus souple consiste à utiliser une entité apprise par la machine avec une liste d’expressions comme descripteur. Cette méthode permet à LUIS d’apprendre et d’étendre les valeurs des données à extraire.

## <a name="import-example-json-and-add-utterances"></a>Importer l’exemple .json et ajouter des énoncés

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. L’application importée a une intention `OrderPizza`. Sélectionnez cette intention et ajoutez quelques énoncés avec de nouveaux types de croûte :

    |Nouveaux énoncés|
    |--|--|
    |please order a pan crust small pepperoni pizza|
    |3 thin crust hawaiian pizzas|
    |deliver 2 stuffed crust pizzas with bread sticks|
    |one thick crust pizza for pickup|
    |one deep dish pepperoni pizza|

## <a name="crust-list-entity"></a>Entité de liste pour les croûtes

Maintenant que l’intention **OrderPizza** a des exemples d’énoncés avec des types de croûte, LUIS doit comprendre quels mots représentent les types de croûte.

Voici des exemples de noms principaux et de synonymes :

|Nom canonique|Synonymes|
|--|--|
|Deep dish|deep<br>deep dish crust<br>thick<br>thick crust|
|Pan|regular<br>ressource d’origine<br>normal<br>regular crust<br>original crust<br>normal crust|
|Stuffed|stuffed crust|
|Thin|thin crust<br>skinny<br>skinny crust|

1. Dans le panneau gauche, sélectionnez **Entités**.

1. Sélectionnez **+ Créer**.

1. Dans la fenêtre de dialogue contextuelle des entités, saisir `CrustList` dans le nom d’entité, et **List** pour le type d’entité. Sélectionnez **Suivant**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la fenêtre contextuelle de création d’une entité](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Dans la page **Créer une entité de liste**, entrez les noms canoniques et les synonymes pour chaque nom canonique, puis sélectionnez **Créer**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’ajout d’éléments à l’entité de liste](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Quand vous ajoutez une entité de liste à une application LUIS, vous n’avez pas besoin d’[étiqueter](label-entity-example-utterance.md) le texte avec l’entité de liste. Elle est appliquée à tous les énoncés dans toutes les intentions.

## <a name="train-the-app-before-testing-or-publishing"></a>Entraîner l’application avant un test ou la publication

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publier l’application vers la requête à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez l’énoncé suivant :

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Le dernier paramètre de la chaîne de requête est `query`, l’énoncé est **query**.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Les types de croûte ont été trouvés comme correspondances exactes de texte et retournés dans la réponse JSON. Ces informations sont utilisées par l’application cliente pour traiter la commande.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informations connexes

* Informations conceptuelles sur les [entités de liste](luis-concept-entity-types.md#list-entity)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)
* [Concept - Entités](luis-concept-entity-types.md)
* [Informations de référence JSON sur l’entité d’expression régulière](reference-entity-regular-expression.md?tabs=V3)
* [Guide pratique pour ajouter des entités pour extraire des données](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez ajouté des exemples d’énoncés, puis créé une entité de liste pour extraire des correspondances de texte exactes à partir des énoncés. Une fois l’application entraînée et publiée, une requête au point de terminaison a identifié l’intention et retourné les données extraites.

> [!div class="nextstepaction"]
> [Ajouter une entité prédéfinie avec un rôle](tutorial-entity-roles.md)

