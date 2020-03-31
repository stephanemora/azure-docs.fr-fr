---
title: Obtenir l’intention avec un appel REST en Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 02cb7738e20df6aba8690c9fe2ee718144bad114
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987769"
---
## <a name="prerequisites"></a>Conditions préalables requises

* Langage de programmation [GO](https://golang.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID d’application public : `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Créer une clé Runtime LUIS pour les prédictions

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur [Créer**Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Entrez tous les paramètres obligatoires pour la clé **Runtime** :

    |Paramètre|Valeur|
    |--|--|
    |Name|Nom de votre choix (2-64 caractères)|
    |Subscription|Sélectionner l’abonnement approprié|
    |Location|Sélectionnez n’importe quel emplacement disponible et proche|
    |Niveau de tarification|`F0` - le niveau tarifaire minimal|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Après sa création, accédez à la page de ressources.
1. Collectez la valeur `endpoint` configurée et une valeur `key`.

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Utilisez Go pour interroger le [point de terminaison de prédiction](https://aka.ms/luis-apim-v3-prediction) afin d’obtenir le résultat de la prédiction.

1. Créez un nouveau fichier appelé `predict.go`. Ajoutez le code suivant :

    ```go
    package main

    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {

        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"

        // utterance for public app
        var utterance = "turn on all lights"

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {

        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))

        response, err := http.Get(endpointUrl)

        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }

        response2, err2 := ioutil.ReadAll(response.Body)

        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }

        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. Remplacez les valeurs `YOUR-KEY` et `YOUR-ENDPOINT` par votre clé **Runtime** et votre point de terminaison de prédiction.

    |Information|Objectif|
    |--|--|
    |`YOUR-KEY`|Votre clé **Runtime** de prédiction (32 caractères).|
    |`YOUR-ENDPOINT`| L’URL de votre point de terminaison de prédiction. Par exemple : `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Avec une invite de commandes située dans le même répertoire que celui où vous avez créé le fichier, entrez la commande suivante pour compiler le fichier Go :

    ```console
    go build predict.go
    ```

1. Exécutez l’application GO à partir de la ligne de commande en entrant le texte suivant dans l’invite de commandes :

    ```console
    go run predict.go
    ```

    La réponse de l’invite de commandes est :

    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON mis en forme pour la lisibilité :

    ```json
    {
        "query": "turn on all lights",
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
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
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

Lorsque vous aurez fini de suivre ce guide de démarrage rapide, supprimez le fichier du système de fichiers.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés et effectuer l’apprentissage de](../get-started-get-model-rest-apis.md)