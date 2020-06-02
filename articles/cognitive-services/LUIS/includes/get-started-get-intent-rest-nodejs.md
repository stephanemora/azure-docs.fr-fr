---
title: Obtenir l’intention avec un appel REST dans Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: f60b4391f5b68f163eb2e97153667d82454639d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654325"
---
## <a name="prerequisites"></a>Prérequis

* Langage de programmation [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-pizza-app"></a>Créer une application de pizza

[!INCLUDE [Create pizza app](get-started-get-intent-create-pizza-app.md)]

## <a name="create-the-nodejs-project"></a>Créer le projet Node.js

1. Créez un dossier pour stocker votre projet Node.js, par exemple `node-predict-with-rest`.

1. Ouvrez une nouvelle invite de commandes, accédez au dossier que vous avez créé et exécutez la commande suivante :

    ```console
    npm init
    ```

    Appuyez sur Entrée à chaque invite pour accepter les paramètres par défaut.

1. Installez les dépendances en entrant les commandes suivantes :

    ```console
    npm install --save request
    npm install --save request-promise
    npm install --save querystring
    ```

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Utilisez Node.js pour interroger le [point de terminaison de prédiction](https://aka.ms/luis-apim-v3-prediction) afin d’obtenir le résultat de la prédiction.

1. Copiez l’extrait de code suivant dans le fichier nommé `predict.js` :

    ```javascript
    var requestPromise = require('request-promise');
    var queryString = require('querystring');

    // Analyze a string utterance.
    getPrediction = async () => {

        //////////
        // Values to modify.

        // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
        const LUIS_appId = "YOUR-APP-ID";

        // YOUR-PREDICTION-KEY: Your LUIS authoring key, 32 character value.
        const LUIS_predictionKey = "YOUR-PREDICTION-KEY";

        // YOUR-PREDICTION-ENDPOINT: Replace this with your authoring key endpoint.
        // For example, "https://westus.api.cognitive.microsoft.com/"
        const LUIS_endpoint = "YOUR-PREDICTION-ENDPOINT";

        // The utterance you want to use.
        const utterance = "I want a deep dish supreme pizza with extra cheese, hold the onions.";
        //////////

        // Create query string
        const queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": LUIS_predictionKey
        }

        // Create the URI for the REST call.
        const URI = `${LUIS_endpoint}luis/prediction/v3.0/apps/${LUIS_appId}/slots/production/predict?${queryString.stringify(queryParams)}`

        // Send the REST call.
        const response = await requestPromise(URI);

        // Display the response from the REST call.
        console.log(response);
    }

    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Remplacez les valeurs `YOUR-KEY` et `YOUR-ENDPOINT` par votre clé **Runtime** et votre point de terminaison de prédiction.

    |Information|Objectif|
    |--|--|
    |`YOUR-KEY`|Votre clé **Runtime** de prédiction (32 caractères).|
    |`YOUR-ENDPOINT`| L’URL de votre point de terminaison de prédiction. Par exemple : `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

 1. Examinez la réponse de prédiction, qui est retournée au format JSON :

    ```json
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
        ```

    The JSON response formatted for readability:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
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

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous aurez fini de suivre ce guide de démarrage rapide, supprimez le dossier de projet du système de fichiers.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés et effectuer l’apprentissage de](../get-started-get-model-rest-apis.md)