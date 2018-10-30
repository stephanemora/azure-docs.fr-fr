---
title: 'Guide de démarrage rapide : Identifier la langue du texte, Go - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez identifier la langue du texte source à l’aide de l’API de traduction de texte Translator Text avec Go.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: erhopf
ms.openlocfilehash: 0275b408e71ec967f6453c94566b4799b3dd4396
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647202"
---
# <a name="quickstart-identify-language-from-text-with-the-translator-text-rest-api-go"></a>Guide de démarrage rapide : identifier la langue du texte à l’aide de l’API de traduction de texte Translator Text (Go)

Dans ce démarrage rapide, vous identifiez la langue du texte source à l’aide de l’API de traduction de texte Translator Text.

## <a name="prerequisites"></a>Prérequis

Pour exécuter ce code, vous devez installer la [distribution Go](https://golang.org/doc/install). Cet exemple de code utilisant uniquement les bibliothèques **principales**, il n’existe aucune dépendance externe.

Pour utiliser l’API de traduction de texte Translator Text, vous avez également besoin d’une clé d’abonnement. Consultez [Comment s’inscrire à l’API de traduction de texte Translator Text](translator-text-how-to-signup.md).

## <a name="detect-request"></a>Requête Détecter

Le code suivant identifie la langue du texte source en utilisant la méthode [Détecter](./reference/v3-0-detect.md).

1. Créez un projet Go dans votre éditeur de code favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `subscriptionKey` par une clé d’accès valide pour votre abonnement.
4. Enregistrez le fichier avec une extension .go.
5. Ouvrez une invite de commandes sur un ordinateur sur lequel Go est installé.
6. Créez le fichier, par exemple : « go build quickstart-detect.go ».
7. Exécutez le fichier, par exemple : « quickstart-detect ».

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/detect?api-version=3.0"

    const uri = uriBase + uriPath

    const text = "Salve, mondo!"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="detect-response"></a>Réponse de détection

Une réponse correcte est renvoyée au format JSON, comme dans l’exemple suivant :

```json
[
  {
    "alternatives": [
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "pt",
        "score": 1
      },
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "en",
        "score": 1
      }
    ],
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "language": "it",
    "score": 1
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez des packages Go pour les API Cognitive Services à partir de [Azure SDK pour Go](https://github.com/Azure/azure-sdk-for-go) sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des packages Go sur GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
