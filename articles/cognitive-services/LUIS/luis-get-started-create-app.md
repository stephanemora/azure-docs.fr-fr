---
title: 'Démarrage rapide : Créer votre application dans le portail LUIS'
description: Ce guide de démarrage rapide montre comment créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appareils électriques. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 04/14/2021
ms.openlocfilehash: 3de83151aa00d589c470eb7ac772f4c9b5f7eda2
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107948336"
---
# <a name="quickstart-build-your-app-in-luis-portal"></a>Démarrage rapide : Créer votre application dans le portail LUIS

Dans ce guide de démarrage rapide, vous créez une application LUIS qui utilise le domaine de domotique prédéfini pour allumer et éteindre des lumières et des appareils. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. Ensuite, essayez de personnaliser votre application en ajoutant des intentions et des entités supplémentaires. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Créer une application

Vous pouvez créer et gérer vos applications sur la page **Mes applications**.

### <a name="create-an-application"></a>Créer une application

Pour créer une application, cliquez sur **+ Nouvelle application**. 

Dans la fenêtre qui s’affiche, entrez les informations suivantes :

|Name  |Description  |
|---------|---------|
|Name     | Nom de votre application. Par exemple, « domotique ».        |
|culture     | Langage que votre application comprend et parle.   |
|Description | Description de votre application.
|Ressource de prédiction | Ressource de prédiction qui recevra les requêtes. |

Sélectionnez **Terminé**.

>[!NOTE]
>La culture ne peut pas être modifiée une fois que l’application est créée.

## <a name="add-prebuilt-domain"></a>Ajouter un domaine prédéfini

LUIS offre un ensemble de domaines prédéfinis qui peuvent vous aider à démarrer avec votre application. Une application de domaine prédéfini est déjà remplie avec des [intentions](./luis-concept-intent.md), des [entités](./luis-concept-entity-types.md) et des [énoncés](./luis-concept-utterance.md).

1. Dans le volet de navigation de gauche, sélectionnez **Domaines prédéfinis**.
2. Recherchez **HomeAutomation**.
3. Sélectionnez **Ajouter un domaine** sur la carte HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Sélectionnez « Domaines prédéfinis », puis recherchez « HomeAutomation ». Sélectionnez « Ajouter un domaine » sur la carte « HomeAutomation ».](media/luis-quickstart-new-app/home-automation.png)

    Une fois le domaine ajouté, la zone de domaine prédéfini affiche un bouton **Supprimer le domaine**.

## <a name="check-out-intents-and-entities&quot;></a>Vérifier les intentions et les entités

1. Sélectionnez **Intentions** dans le volet de navigation de gauche pour consulter les intentions du domaine HomeAutomation. Il contient des exemples d’énoncés, tels que `HomeAutomation.QueryState` et     `HomeAutomation.SetDevice`.

    > [!NOTE]
    > **Aucun** est une intention fournie par toutes les applications LUIS. Elle vous permet de gérer les énoncés qui ne correspondent pas aux fonctionnalités fournies par votre application.

2. Sélectionnez l’intention **HomeAutomation.TurnOff**. L’intention contient une liste d’exemples d’énoncés qui sont associés à des entités.

    > [!div class=&quot;mx-imgBorder&quot;]
    > [![Capture d’écran de l’intention HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png &quot;Capture d’écran de l’intention HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

3. Si vous souhaitez afficher les entités pour l’application, sélectionnez **Entités**. Si vous cliquez sur l’une des entités, par exemple **HomeAutomation.DeviceName**, vous verrez la liste des valeurs qui lui sont associées. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Texte de l’image associée" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS
Une fois que votre application est renseignée avec des intentions, des entités et des énoncés, vous devez entraîner l’application pour refléter les changements effectués.

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Test de l'application

Une fois que vous avez formé votre application, vous pouvez la tester.

1. Sélectionnez **Test** dans le menu en haut à droite.

1. Tapez un énoncé de test dans le volet de test interactif et appuyez sur Entrée. Par exemple, *Turn off the lights* (Éteindre les lumières).

    Dans cet exemple, *Turn off the lights* est identifié comme l’intention avec le score le plus élevé pour **HomeAutomation.TurnOff**.

    :::image type="content" source="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png" alt-text="Capture d’écran du panneau de test avec l’énoncé mis en surbrillance" lightbox="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png":::

1. Sélectionnez **Inspecter** pour voir plus d’informations sur la prédiction.

    :::image type="content" source="media/luis-quickstart-new-app/test.png" alt-text="Capture d’écran du panneau de test avec les informations d’inspection" lightbox="media/luis-quickstart-new-app/test.png":::

1. Fermez le volet de test.

## <a name="customize-your-application"></a>Personnaliser votre application

En plus des domaines prédéfinis, LUIS vous permet de créer vos propres applications personnalisées ou de personnaliser celles prédéfinies.

### <a name="create-intents"></a>Créer des intentions

Pour ajouter d’autres intentions à votre application

1. Sélectionnez **Intentions** dans le menu de navigation gauche.
2. Sélectionnez **Créer**
3. Entrez le nom de l’intention, `HomeAutomation.AddDeviceAlias`, puis sélectionnez Terminé.

### <a name="create-entities"></a>Créer des entités

Pour ajouter d’autres entités à votre application

1. Sélectionnez **Entités** dans le menu de navigation gauche.
2. Sélectionnez **Créer**
3. Entrez le nom de l’intention, `HomeAutomation.DeviceAlias`, sélectionnez le **type** machine learning, puis sélectionnez **Créer**.

### <a name="add-example-utterances"></a>Ajouter des exemples d’énoncés

Les exemples d'énoncés sont du texte qu’un utilisateur entre dans un bot conversationnel ou une autres application cliente. Ils mappent l’intention du texte de l’utilisateur sur une intention LUIS.

Dans la page **Intentions** pour `HomeAutomation.AddDeviceAlias`, ajoutez les exemples d’énoncé suivants sous **Exemple d’énoncé** :

|#|Exemples d’énoncés|
|--|--|
|1|`Add alias to my fan to be wind machine`|
|2|`Alias lights to illumination`|
|3|`nickname living room speakers to our speakers a new fan`|
|4|`rename living room tv to main tv`|

[!INCLUDE [best-practice-utterances](./includes/best-practice-utterances.md)]


### <a name="label-example-utterances"></a>Étiqueter les exemples d’énoncés

L’étiquetage de vos énoncés est nécessaire, car vous avez ajouté une entité ML. L’étiquetage est utilisé par votre application pour apprendre à extraire les entités ML que vous avez créées.

[!INCLUDE [how-to-label](./includes/how-to-label.md)]

## <a name="create-prediction-resource"></a>Créer une ressource de prédiction
À ce stade, vous avez terminé de créer votre application. Vous devez créer une ressource de prédiction pour publier votre application afin de recevoir des prédictions dans un chatbot ou une autre application cliente via le point de terminaison de prédiction

Pour créer une ressource de prédiction à partir du portail LUIS

[!INCLUDE [add-pred-resource-portal](./includes/add-prediction-resource-portal.md)]


## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]



## <a name="query-the-v3-api-prediction-endpoint"></a>Interroger le point de terminaison de prédiction d’API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Dans la barre d’adresse du navigateur, pour la chaîne de requête, vérifiez que les valeurs suivantes figurent dans l’URL. Si elles ne figurent pas dans la chaîne de requête, ajoutez-les :

    * `verbose=true`
    * `show-all-intents=true`

3. Dans la barre d’adresse du navigateur, accédez à la fin de l’URL et entrez *turn off the living room light* pour la chaîne de requête, puis appuyez sur Entrée.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
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

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Conception d’application itérative](./luis-concept-app-iteration.md)
* [Bonnes pratiques](./luis-concept-best-practices.md)
