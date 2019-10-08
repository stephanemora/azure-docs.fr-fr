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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703600"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Démarrage rapide : Reconnaître une intention avec un navigateur

Pour comprendre ce que retourne un point de terminaison de prédiction LUIS, affichez un résultat de prédiction dans un navigateur web. 

## <a name="prerequisites"></a>Prérequis

Pour pouvoir interroger une application publique, voici ce dont vous avez besoin :

* Votre propre clé LUIS (Language Understanding). Si vous n’avez pas encore d’abonnement pour créer une clé, vous pouvez vous inscrire pour obtenir un [compte gratuit](https://azure.microsoft.com/free/).
* L’ID de l’application publique : `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Utiliser le navigateur pour voir des prédictions

1. Ouvrez un navigateur web. 
1. Utilisez les URL complètes ci-dessous, en remplaçant `{your-key}` par votre propre clé LUIS. Les demandes sont des demandes GET qui incluent l’autorisation, avec votre clé LUIS, en tant que paramètre de chaîne de requête.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[Demande de point de terminaison de prédiction V2](#tab/V2)
    
    Le format de l’URL V2 pour une demande de point de terminaison **GET** est :
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[Demande de point de terminaison de prédiction V3](#tab/V3)
    
    
    Le format de l’URL V3 pour une demande de point de terminaison **GET** (par les emplacements) est :
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Collez l’URL dans une fenêtre de navigateur, puis appuyez sur Entrée. Le navigateur affiche un résultat JSON qui indique que LUIS a détecté l’intention `HomeAutomation.TurnOn` comme première intention et l’entité `HomeAutomation.Operation` pourvue de la valeur `on`.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
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

    * * *

1. Pour voir toutes les intentions, ajoutez le paramètre de chaîne de requête approprié. 

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

    #### <a name="v3-prediction-endpointtabv3"></a>[Point de terminaison de prédiction V3](#tab/V3)

    Ajoutez `show-all-intents=true` à la fin de la chaîne de requête pour **afficher toutes les intentions** :

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
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

    * * * 

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Créer une application dans le portail LUIS](get-started-portal-build-app.md)