---
title: 'Démarrage rapide : Créer une application - LUIS'
titleSuffix: Azure Cognitive Services
description: Créez une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appliances. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1704b62cae6375d376fc43fb7a2940cd9c717072
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382502"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Démarrage rapide : Utiliser une application domotique prédéfinie

Dans ce guide de démarrage rapide, vous allez créer une application LUIS qui utilise le domaine prédéfini `HomeAutomation` pour allumer et éteindre des lumières et des appareils électriques. Ce domaine prédéfini vous fournit les intentions, les entités et des exemples d’énoncés. À la fin du processus, vous disposerez d’un point de terminaison LUIS exécuté dans le cloud.

## <a name="prerequisites"></a>Prérequis

Pour cet article, vous devez disposer d’un compte LUIS gratuit que vous pouvez créer depuis le portail LUIS à l’adresse [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Créer une application
Vous pouvez créer et gérer vos applications sur la page **Mes applications**. 

2. Sélectionnez **Créer une application**.

    [![Capture d’écran de liste d’applications](media/luis-quickstart-new-app/app-list.png "Capture d’écran de liste d’applications")](media/luis-quickstart-new-app/app-list.png)

3. Dans la boîte de dialogue, nommez votre application « HomeAutomation ».

    [![Capture d’écran de la boîte de dialogue contextuelle Créer une application](media/luis-quickstart-new-app/create-new-app-dialog.png "Capture d’écran de la boîte de dialogue contextuelle Créer une application")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Choisissez la culture de votre application. Pour cette application HomeAutomation, sélectionnez Anglais. Ensuite, sélectionnez **Terminé**. LUIS crée l’application HomeAutomation. 

    >[!NOTE]
    >La culture ne peut pas être modifiée une fois que l’application est créée. 

## <a name="add-prebuilt-domain"></a>Ajouter un domaine prédéfini

Sélectionnez **Domaines prédéfinis** dans le volet de navigation de gauche. Lancez une recherche sur le terme « Home ». Sélectionnez **Ajouter un domaine**.

[![Capture d’écran du domaine de domotique appelé dans le menu de domaine prédéfini](media/luis-quickstart-new-app/home-automation.png "Capture d’écran du domaine de domotique appelé dans le menu de domaine prédéfini")](media/luis-quickstart-new-app/home-automation.png)

Une fois le domaine ajouté, la zone de domaine prédéfini affiche un bouton **Supprimer le domaine**.

[![Capture d’écran du domaine de domotique avec le bouton Supprimer](media/luis-quickstart-new-app/remove-domain.png "Capture d’écran du domaine de domotique avec le bouton Supprimer")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intentions et entités

Sélectionnez **Intentions** dans le volet de navigation de gauche pour afficher les intentions du domaine HomeAutomation. Chaque intention comprend des exemples d’énoncés.

![Capture d’écran de la liste d’intentions HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Capture d’écran de la liste d’intentions HomeAutomation")]

> [!NOTE]
> **Aucun** est une intention fournie par toutes les applications LUIS. Elle vous permet de gérer les énoncés qui ne correspondent pas aux fonctionnalités fournies par votre application. 

Sélectionnez l’intention **HomeAutomation.TurnOff**. Vous pouvez voir que l’intention contient une liste d’énoncés qui sont associés à des entités.

[![Capture d’écran de l’intention HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Capture d’écran de l’intention HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Test de l'application
Une fois que vous avez formé votre application, vous pouvez la tester. Sélectionnez **Tester** dans la barre de navigation supérieure. Saisissez un énoncé de test tel que « Éteindre les lumières » dans le volet Test interactif, puis appuyez sur Entrée. 

```
Turn off the lights
```

Vérifiez que l’intention avec le score le plus élevé correspond à l’intention attendue pour chaque énoncé de test.

Dans cet exemple, `Turn off the lights` est identifié correctement comme l’intention avec le score le plus élevé pour **HomeAutomation.TurnOff**.

[![Capture d’écran du panneau Tester avec énoncé mis en surbrillance](media/luis-quickstart-new-app/test.png "Capture d’écran du panneau Tester avec énoncé mis en surbrillance")](media/luis-quickstart-new-app/test.png)


Sélectionnez **Inspecter** pour passer en revue des informations supplémentaires sur la prédiction.

![Capture d’écran du panneau Test avec l’énoncé mis en surbrillance](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Sélectionnez à nouveau **Tester** pour réduire le volet de test. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Interroger le point de terminaison de prédiction d’API V2

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Accédez à la fin de l’URL dans la barre d’adresses, entrez `turn off the living room light`, puis appuyez sur Entrée. Le navigateur affiche la version d’**API V2** de la réponse JSON de votre point de terminaison HTTP.

    ```json
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.9753089
      },
      "intents": [
        {
          "intent": "HomeAutomation.TurnOff",
          "score": 0.9753089
        },
        {
          "intent": "HomeAutomation.QueryState",
          "score": 0.01027893
        },
        {
          "intent": "HomeAutomation.TurnUp",
          "score": 0.006881481
        },
        {
          "intent": "HomeAutomation.SetDevice",
          "score": 0.006786365
        },
        {
          "intent": "HomeAutomation.TurnDown",
          "score": 0.005145787
        },
        {
          "intent": "HomeAutomation.TurnOn",
          "score": 0.004114749
        },
        {
          "intent": "None",
          "score": 0.000598924
        }
      ],
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Location",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.94558233
        },
        {
          "entity": "living room light",
          "type": "HomeAutomation.DeviceName",
          "startIndex": 13,
          "endIndex": 29,
          "resolution": {
            "values": [
              "living room light"
            ]
          }
        },
        {
          "entity": "light",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 25,
          "endIndex": 29,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
## <a name="query-the-v3-api-prediction-endpoint"></a>Interroger le point de terminaison de prédiction d’API V3

Pour une [requête d’API V3](luis-migration-api-v3.md), dans le navigateur, modifiez la requête HTTPS de la méthode GET, en remplaçant les valeurs entre crochets pointus par vos propres valeurs. 

**URL V2 avec méthode GET** :

https://\<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/\<ID_application>?verbose=true&subscription-key=\<VOTRE_CLÉ>&**q=\<user-utterance-text>**

**URL V3 avec méthode GET** :

https://\<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/\<ID_application>/**slots**/**production**/**predict**?verbose=true&subscription-key=\<VOTRE_CLÉ>&**query=\<user-utterance-text>**

Le navigateur affiche la version d’**API V3** de la réponse JSON de votre point de terminaison HTTP.

```json
{
    "query": "turn off the lights",
    "prediction": {
        "normalizedQuery": "turn off the lights",
        "topIntent": "HomeAutomation.TurnOff",
        "intents": {
            "HomeAutomation.TurnOff": {
                "score": 0.99649024
            }
        },
        "entities": {
            "HomeAutomation.DeviceType": [
                [
                    "light"
                ]
            ],
            "$instance": {
                "HomeAutomation.DeviceType": [
                    {
                        "type": "HomeAutomation.DeviceType",
                        "text": "lights",
                        "startIndex": 13,
                        "length": 6,
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

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez appeler le point de terminaison à partir du code :

> [!div class="nextstepaction"]
> [Appeler un point de terminaison LUIS à l’aide d’un code](luis-get-started-cs-get-intent.md)
