---
title: 'Démarrage rapide : Créer une application - LUIS'
description: Ce guide de démarrage rapide montre comment créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appareils électriques. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: cefa9d5995f876ef3f07cc32324e747125e9cbf7
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701293"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Démarrage rapide : Utiliser une application domotique prédéfinie

Dans ce guide de démarrage rapide, vous allez créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appareils électriques. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Créer une application
Vous pouvez créer et gérer vos applications sur la page **Mes applications**.

1. Dans la liste Mes applications, sélectionnez **+Nouvelle application de conversation**, puis dans la liste des options, sélectionnez à nouveau **+Nouvelle application de conversation**.

1. Dans la boîte de dialogue, nommez votre application `Home Automation`.
1. Sélectionnez **English** comme culture.
1. Entrer une description facultative.
1. Ne sélectionnez pas de ressource de prédiction si vous n’avez pas encore créé la ressource. Pour utiliser le point de terminaison de prédiction de votre application (intermédiaire ou production), vous devez affecter une ressource de prédiction.
1. Sélectionnez **Terminé**.

    LUIS crée l’application.

    ![Dans la boîte de dialogue, nommez votre application « Home Automation ».](./media/create-new-app-details.png)

    >[!NOTE]
    >La culture ne peut pas être modifiée une fois que l’application est créée.

## <a name="add-prebuilt-domain"></a>Ajouter un domaine prédéfini

1. Dans le volet de navigation de gauche, sélectionnez **Domaines prédéfinis**.
1. Recherchez **HomeAutomation**.
1. Sélectionnez **Ajouter un domaine** sur la carte HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Sélectionnez « Domaines prédéfinis », puis recherchez « HomeAutomation ». Sélectionnez « Ajouter un domaine » sur la carte « HomeAutomation ».](media/luis-quickstart-new-app/home-automation.png)

    Une fois le domaine ajouté, la zone de domaine prédéfini affiche un bouton **Supprimer le domaine**.

## <a name="intents-and-entities"></a>Intentions et entités

1. Sélectionnez **Intentions** pour passer en revue les intentions du domaine HomeAutomation. Les intentions de domaine prédéfini comportent des exemples d’énoncés.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la liste des intentions de HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Capture d’écran de la liste des intentions de HomeAutomation")

    > [!NOTE]
    > **Aucun** est une intention fournie par toutes les applications LUIS. Elle vous permet de gérer les énoncés qui ne correspondent pas aux fonctionnalités fournies par votre application.

1. Sélectionnez l’intention **HomeAutomation.TurnOff**. L’intention contient une liste d’exemples d’énoncés qui sont associés à des entités.

    > [!div class="mx-imgBorder"]
    > [![Capture d’écran de l’intention HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Capture d’écran de l’intention HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Test de l'application
Une fois que vous avez formé votre application, vous pouvez la tester.

1. Sélectionnez **Test** dans le menu en haut à droite.

1. Saisissez un énoncé de test tel que `Turn off the lights` dans le volet de test interactif, puis appuyez sur Entrée.

    ```
    Turn off the lights
    ```

    Dans cet exemple, `Turn off the lights` est identifié correctement comme l’intention avec le score le plus élevé pour **HomeAutomation.TurnOff**.

    ![Capture d’écran du panneau Test avec l’énoncé mis en surbrillance](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Sélectionnez **Inspecter** pour voir plus d’informations sur la prédiction.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du panneau Test avec les informations d’inspection](media/luis-quickstart-new-app/test.png)

1. Fermez le volet de test.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Interroger le point de terminaison de prédiction d’API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Dans la barre d’adresse du navigateur, pour la chaîne de requête, assurez-vous que les barres de noms et de valeurs suivantes figurent dans l’URL. Si elles ne figurent pas dans la chaîne de requête, ajoutez-les :

    |Paire nom/valeur|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Dans la barre d’adresse du navigateur, allez à la fin de l’URL et entrez `turn off the living room light` pour la valeur _requête_, puis appuyez sur Entrée.

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

Vous pouvez appeler le point de terminaison à partir du code :

> [!div class="nextstepaction"]
> [Appeler un point de terminaison LUIS à l’aide d’un code](luis-get-started-cs-get-intent.md)
