---
title: 'Démarrage rapide : Créer une application - LUIS'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appareils électriques. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 302321a36a6ce7526ad5e3144f87b88edbfaaec7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448099"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Démarrage rapide : Utiliser une application domotique prédéfinie

Dans ce guide de démarrage rapide, vous allez créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appareils électriques. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Créer une application
Vous pouvez créer et gérer vos applications sur la page **Mes applications**.

1. Dans le portail LUIS, dans la liste Mes applications, sélectionnez **+ Créer**.

    ![Dans le portail LUIS, dans la liste Mes applications, sélectionnez « + Créer ».](./media/create-app-in-portal.png)

1. Dans la boîte de dialogue, nommez votre application `Home Automation`, puis sélectionnez **Terminé**. LUIS crée l’application. La description est facultative et n’est pas utilisée pour la création ou la prédiction. La ressource de prédiction est également facultative lors de la création d’une application LUIS. Lorsque vous publiez votre application en production, vous devez affecter une ressource de prédiction afin que votre application puisse gérer de nombreuses requêtes.

    ![Dans la boîte de dialogue, nommez votre application « Home Automation ».](./media/create-new-app-details.png)

    >[!NOTE]
    >La culture ne peut pas être modifiée une fois que l’application est créée.

## <a name="add-prebuilt-domain"></a>Ajouter un domaine prédéfini

Sélectionnez **Domaines prédéfinis**, puis recherchez **HomeAutomation**. Sélectionnez **Ajouter un domaine** sur la carte HomeAutomation.

![Sélectionnez « Domaines prédéfinis », puis recherchez « HomeAutomation ». Sélectionnez « Ajouter un domaine » sur la carte « HomeAutomation ».](media/luis-quickstart-new-app/home-automation.png)

Une fois le domaine ajouté, la zone de domaine prédéfini affiche un bouton **Supprimer le domaine**.

## <a name="intents-and-entities"></a>Intentions et entités

Sélectionnez **Intentions** pour passer en revue les intentions du domaine HomeAutomation. Les intentions de domaine prédéfini comportent des exemples d’énoncés.

![Capture d’écran de la liste des intentions de HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Capture d’écran de la liste des intentions de HomeAutomation")

> [!NOTE]
> **Aucun** est une intention fournie par toutes les applications LUIS. Elle vous permet de gérer les énoncés qui ne correspondent pas aux fonctionnalités fournies par votre application.

Sélectionnez l’intention **HomeAutomation.TurnOff**. Vous pouvez voir que l’intention contient une liste d’énoncés qui sont associés à des entités.

[![Capture d’écran de l’intention HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Capture d’écran de l’intention HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Test de l'application
Une fois que vous avez formé votre application, vous pouvez la tester. Sélectionnez **Test**. Saisissez un énoncé de test tel que `Turn off the lights` dans le volet de test interactif, puis appuyez sur Entrée.

```
Turn off the lights
```

Vérifiez que l’intention avec le score le plus élevé correspond à l’intention attendue pour chaque énoncé de test.

Dans cet exemple, `Turn off the lights` est identifié correctement comme l’intention avec le score le plus élevé pour **HomeAutomation.TurnOff**.

![Capture d’écran du panneau Test avec l’énoncé mis en surbrillance](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Sélectionnez **Inspecter** pour passer en revue des informations supplémentaires sur la prédiction.

![Capture d’écran du panneau Test avec les informations d’inspection](media/luis-quickstart-new-app/test.png)

Sélectionnez à nouveau **Tester** pour réduire le volet de test.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Interroger le point de terminaison de prédiction d’API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

1. Dans la barre d’adresse du navigateur, pour la chaîne de requête, assurez-vous que les barres de noms et de valeurs suivantes figurent dans l’URL. Si elles ne figurent pas dans la chaîne de requête, ajoutez-les :

    |Paire nom/valeur|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. Dans la barre d’adresse du navigateur, allez à la fin de l’URL et entrez `turn off the living room light` pour la valeur _requête_, puis appuyez sur Entrée.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
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
                            "score": 0.907323956,
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
