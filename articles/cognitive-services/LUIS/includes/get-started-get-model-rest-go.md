---
title: Obtenir un modèle avec un appel REST dans Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: ec61abca19579426818e227687e08e66b73969cb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505850"
---
## <a name="prerequisites"></a>Prérequis

* Clé de démarrage.
* Importez l’application [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) à partir du référentiel GitHub cognitive-services-language-understanding.
* L’ID d’application LUIS pour l’application TravelAgent importée. L’ID d’application est indiqué dans le tableau de bord de l’application.
* L’ID de version dans l’application qui reçoit les énoncés. L’ID par défaut est « 0.1 ».
* Langage de programmation [GO](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Modifier le modèle par programmation

Utilisez Go pour ajouter à l’application une [API](https://aka.ms/luis-apim-v3-authoring) d’entité issue de l’apprentissage automatique. 

1. Créez un nouveau fichier appelé `predict.go`. Ajoutez le code suivant :
    
    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )
    
    // main function
    func main() {
    
        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"
    
        // NOTE: change to your starter key
        var authoringKey = "YOUR-KEY"
    
        // NOTE: change to your starter key's endpoint, for example, westus.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"  
    
        var version = "0.1"
    
        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `
    
        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)
    
        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)
    
        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }
    
    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)
    
        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){
    
        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)
    
        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){
    
        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){
    
        client := &http.Client{}
    
        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)
    
        fmt.Println("body")
        fmt.Println(body)
    
        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }    
    ```

1. Remplacez les valeurs suivantes :

    * `YOUR-KEY` avec votre clé de démarrage
    * `YOUR-ENDPOINT` avec votre point de terminaison, par exemple, `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` avec l’ID de votre application

1. Avec une invite de commandes située dans le même répertoire que celui où vous avez créé le fichier, entrez la commande suivante pour compiler le fichier Go :

    ```console
    go build model.go
    ```  

1. Exécutez l’application GO à partir de la ligne de commande en entrant le texte suivant dans l’invite de commandes : 

    ```console
    go run model.go
    ```

## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous aurez fini de suivre ce guide de démarrage rapide, supprimez le fichier du système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour une application](../luis-concept-best-practices.md)