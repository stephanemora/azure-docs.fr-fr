---
title: 'Démarrage rapide : Mettre à jour une base de connaissances - REST, Go - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce démarrage rapide basé sur REST vous aide à mettre à jour votre exemple de base de connaissances QnA Maker par programmation. La définition JSON que vous utilisez pour mettre à jour une base de connaissances vous permet d’ajouter, de modifier ou de supprimer des paires de questions/réponses.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 5275184994dce446a7ca572a049f3860c4782139
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646879"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-go"></a>Démarrage rapide : Mettre à jour une base de connaissances dans QnA Maker à l’aide de Go

Ce démarrage rapide vous aide à mettre à jour une base de connaissances QnA Maker existante par programmation.  Ce code JSON vous permet de mettre à jour une base de connaissances en ajoutant de nouvelles sources de données, en modifiant ou supprimant des sources de données.

Cette API correspond aux actions de modification, puis d’utilisation du bouton **Save and train** (Enregistrer et effectuer l’apprentissage) dans le portail QnA Maker.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) : le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API. 
* [Obtenir les détails de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

1. Créez un projet Go dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "time"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

type Response struct {
    Headers map[string][]string
    Body    string
}

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func patch(uri string, content string) Response {
    req, _ := http.NewRequest("PATCH", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return Response {response.Header, string(body)}
}

func get(uri string) Response {
    req, _ := http.NewRequest("GET", uri, nil)
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    return Response {response.Header, string(body)}
}

var req string = `{
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
}`;

func update_kb (uri string, req string) (string, string) {
    fmt.Println("Calling " + uri + ".")
    result := patch(uri, req)
    return result.Headers["Location"][0], result.Body
}

func check_status(uri string) (string, string) {
    fmt.Println("Calling " + uri + ".")
    result := get(uri)
    if retry, success := result.Headers["Retry-After"]; success {
        return retry[0], result.Body
    } else {
// If the response headers did not include a Retry-After value, default to 30 seconds.
        return "30", result.Body
    }
}

func main() {
    var uri = host + service + method + kb
    operation, body := update_kb (uri, req)
    fmt.Printf(body + "\n")
    var done bool = false
    for done == false {
        uri := host + service + operation
        wait, status := check_status(uri)
        fmt.Println(status)
        var status_obj map[string]interface{}
        json.Unmarshal([]byte(status), &status_obj)
        state := status_obj["operationState"]
// If the operation isn't finished, wait and query again.
        if state == "Running" || state == "NotStarted" {
            fmt.Printf ("Waiting " + wait + " seconds...")
            sec, _ := strconv.Atoi(wait)
            time.Sleep (time.Duration(sec) * time.Second)
        } else {
            done = true
        }
    }
}
```

## <a name="the-update-knowledge-base-response"></a>La réponse de mise à jour de la base de connaissances

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

## <a name="get-request-status"></a>Obtenir l’état d’une demande

Vous pouvez appeler la méthode [Operation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) pour vérifier l’état d’une demande de création ou de mise à jour d’une base de connaissances. Pour voir comment cette méthode est utilisée, examinez l’exemple de code pour le démarrage rapide [Créer](create-new-kb-go.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
