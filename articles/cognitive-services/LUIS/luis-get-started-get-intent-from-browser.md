---
title: 'Démarrage rapide : Reconnaître une intention avec le navigateur - LUIS'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel dans un navigateur.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: e06bb4c09b3ebab25c0c0ef8ac5c51f6842f34cd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987952"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Démarrage rapide : Reconnaître une intention avec un navigateur

Pour comprendre ce que retourne un point de terminaison de prédiction LUIS, affichez un résultat de prédiction dans un navigateur web.

## <a name="prerequisites"></a>Conditions préalables requises

Pour pouvoir interroger une application publique, voici ce dont vous avez besoin :

* Votre propre clé de création ou de prédiction LUIS, que vous pouvez obtenir à partir du [portail LUIS (préversion)](https://preview.luis.ai/). Si vous n’avez pas encore d’abonnement pour créer une clé, vous pouvez vous inscrire pour obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* L’ID de l’application publique : `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="use-the-browser-to-see-predictions"></a>Utiliser le navigateur pour voir des prédictions

1. Ouvrez un navigateur web.
1. Utilisez les URL complètes ci-dessous, en remplaçant `YOUR-KEY` par votre propre clé de création ou de prédiction LUIS. Les requêtes sont des requêtes GET qui incluent l’autorisation, avec votre clé de création ou de prédiction LUIS, en tant que paramètre de chaîne de requête.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[Requête de prédiction V3](#tab/V3-1-1)


    Le format de l’URL V3 pour une demande de point de terminaison **GET** (par les emplacements) est :

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[Requête de prédiction V2](#tab/V2-1-2)

    Le format de l’URL V2 pour une demande de point de terminaison **GET** est :

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Collez l’URL dans une fenêtre de navigateur, puis appuyez sur Entrée. Le navigateur affiche un résultat JSON qui indique que LUIS a détecté l’intention `HomeAutomation.TurnOn` comme première intention et l’entité `HomeAutomation.Operation` pourvue de la valeur `on`.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[Réponse de prédiction V3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[Réponse de prédiction V2](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Pour voir toutes les intentions, ajoutez le paramètre de chaîne de requête approprié.

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[Point de terminaison de prédiction V3](#tab/V3-3-1)

    Ajoutez `show-all-intents=true` à la fin de la chaîne de requête pour **afficher toutes les intentions** :

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpointtabv2"></a>[Point de terminaison de prédiction V2](#tab/V2)

    Ajoutez `verbose=true` à la fin de la chaîne de requête pour **afficher toutes les intentions** :

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Créer une application dans le portail LUIS](get-started-portal-build-app.md)
