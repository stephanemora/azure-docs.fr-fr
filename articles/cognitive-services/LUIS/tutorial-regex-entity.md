---
title: 'Didacticiel : Entité Expression régulière – LUIS'
titleSuffix: Azure Cognitive Services
description: Extrayez des données mises en forme de façon cohérente depuis un énoncé à l’aide de l’entité Expression régulière.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 8da47899e2d3d2c1d04e9cf4768a968e7893ce96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852444"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Didacticiel : Obtenir des données au format approprié à partir de l’énoncé
Dans ce tutoriel, créez une entité d’expression régulière pour extraire des données mises en forme de façon cohérente à partir d’un énoncé.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer une application
> * Ajouter une intention
> * Ajouter une entité d’expression régulière
> * Entraîner, publier et interroger une application pour obtenir des données extraites

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entité d’expression régulière

Utilisez l’entité d’expression régulière pour extraire du texte correctement mis en forme à partir d’un énoncé. Bien que l’intention de l’énoncé soit toujours déterminée avec l’apprentissage automatique, ce type d’entité spécifique n’est pas issu du machine learning. Une bonne utilisation pour l’entité d’expression régulière est tout texte pouvant être représenté de façon cohérente par une [expression régulière](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

Cet exemple utilise un _code court_ pour l’envoi de SMS. Ce code court est un code numérique à 5 ou 6 chiffres, préfixé d’un x et qui peut être décrit avec l’expression régulière `x\d{5,6}`.

Quand vous ajoutez une entité d’expression régulière à une application LUIS, vous n’avez pas besoin d’[étiqueter](label-entity-example-utterance.md) le texte avec l’entité d’expression régulière. Elle est appliquée à tous les énoncés dans toutes les intentions.

## <a name="import-example-json-to-begin-app"></a>Exemple d’importation .json pour commencer une application

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Créer une intention pour l’envoi de SMS de confirmation

1. Sélectionnez **+ Créer** pour créer une nouvelle intention afin de classifier l’intention d’un énoncé pour l’envoi d’un texte de confirmation.

1. Entrez `ConfirmationText` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

1. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Envoyer l’heure de livraison d’une pizza à x123432|
    |Txt x234567 pour l’heure|
    |x23987 pour l’avis|

    Pour extraire des entités apprises par la machine, vous devez fournir des exemples qui incluent l’entité dans une variété d’énoncés, mais avec cette entité non apprise par la machine, la variation n’est pas importante. Dès lors que le texte correspond à l’expression régulière, il est extrait.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Utiliser l’entité d’expression régulière pour les données au format approprié
Créez une entité d’expression régulière correspondant au nombre du texte. Cette expression régulière correspond au texte, mais ignore les variantes de casse et de culture.

1. Dans le panneau gauche, sélectionnez **Entités**.

1. Sélectionnez **Créer** dans la page de la liste des entités.

1. Dans la boîte de dialogue contextuelle, entrez le nouveau nom d’entité `ConfirmationTextRegEx`, sélectionnez **RegEx** comme type d’entité, puis sélectionnez **Suivant**.

    > [!div class="mx-imgBorder"]
    > ![Commencer les étapes de création d’entité pour une entité d’expression régulière](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. Dans la page **Créer une entité d’expression régulière**, entrez `x\d{5,6}` comme valeur de **Expression régulière**, puis sélectionnez **Créer**.

    > [!div class="mx-imgBorder"]
    > ![Entrer une expression régulière pour extraire des données à partir d’un exemple d’énoncé](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Sélectionnez **Intentions** dans le menu de gauche, puis l’intention **ConfirmationText** pour voir l’expression régulière étiquetée dans les énoncés.

    > [!div class="mx-imgBorder"]
    > ![Afficher l’expression régulière étiquetée dans les exemples d’énoncés](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Dans la mesure où l’entité n’est pas une entité issue de la machine, elle est appliquée aux énoncés et affichée dans le portail LUIS dès sa création.

## <a name="train-the-app-before-testing-or-publishing"></a>Entraîner l’application avant un test ou la publication

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publier l’application vers la requête à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez l’énoncé suivant :

    `Text my pizza delivery to x23456 x234567 x12345`

    Le dernier paramètre de la chaîne de requête est `query`, l’énoncé est **query**.

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    En utilisant une entité d’expression régulière, LUIS extrait les données nommées, ce qui est plus utile, sur le plan de la programmation, à l’application cliente qui reçoit la réponse JSON.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informations connexes

* [Concept - Entités](luis-concept-entity-types.md)
* [Informations de référence JSON sur l’entité d’expression régulière](reference-entity-regular-expression.md?tabs=V3)
* [Guide pratique pour ajouter des entités pour extraire des données](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une intention, ajouté des exemples d’énoncés, puis créé une entité d’expression régulière pour extraire des données bien formées à partir des énoncés. Une fois l’application entraînée et publiée, une requête au point de terminaison a identifié l’intention et retourné les données extraites.

> [!div class="nextstepaction"]
> [En savoir plus sur l’entité de liste](tutorial-list-entity.md)

