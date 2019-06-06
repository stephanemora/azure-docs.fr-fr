---
title: 'Démarrage rapide : Traduire du texte, Go - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous traduisez du texte d’une langue à une autre à l’aide de l’API de traduction de texte Translator Text avec Go en moins de 10 minutes.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: cf0a7598d7af583e3339c511556a121523d12a7a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514885"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-go"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text pour traduire une chaîne à l’aide de Go

Dans ce démarrage rapide, vous allez apprendre à traduire une chaîne de texte de l’anglais vers l’italien et l’allemand à l’aide de Go et de l’API REST de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Go](https://golang.org/doc/install)
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Go dans votre IDE ou éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `translate-text.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Créer la fonction main

Cet exemple tentera de lire votre clé d’abonnement Translator Text depuis la variable d’environnement `TRANSLATOR_TEXT_KEY`. Si vous n’êtes pas familiarisé avec les variables d’environnement, vous pouvez définir `subscriptionKey` en tant que chaîne et commentez l’instruction conditionnelle.

Copiez ce code dans votre projet :

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables. If so, be sure to delete the "os" import.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our translate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    translate(subscriptionKey)
}
```

## <a name="create-a-function-to-translate-text"></a>Créer une fonction pour traduire du texte

Créons une fonction pour traduire du texte. Cette fonction n’utilisera qu’un seul argument, votre clé d’abonnement d’API de traduction de texte Translator Text.

```go
func translate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Passons ensuite à la génération de l’URL. L’URL est générée à l’aide des méthodes `Parse()` et `Query()`. Vous remarquerez qu’avec la méthode `Add()`, des paramètres sont ajoutés. Dans cet exemple, vous traduisez de l’anglais vers l’allemand et l’italien : `de` et `it`.

Copiez ce code dans la fonction `translate`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/translate?api-version=3.0")
q := u.Query()
q.Add("to", "de")
q.Add("to", "it")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Pour plus d’informations sur les points de terminaison, les itinéraires et les paramètres de requête, consultez [API de traduction de texte Translator Text 3.0 : Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

## <a name="create-a-struct-for-your-request-body"></a>Créer une structure pour le corps de votre demande

Créez ensuite une structure anonyme pour le corps de la demande et codez-la au format JSON avec `json.Marshal()`. Ajoutez ce code à la fonction `translate`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Hello, world!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Générer la demande

Maintenant que vous avez codé le corps de la demande au format JSON, vous pouvez créer votre requête POST et appeler l’API de traduction de texte Translator Text.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>Gérer et imprimer la réponse

Ajoutez ce code à la fonction `translate` pour décoder la réponse JSON, puis mettez en forme et imprimez le résultat.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Assemblage

Voilà, vous avez installé un programme simple qui appellera l’API de traduction de texte Translator Text et enverra une réponse JSON. Il est maintenant temps d’exécuter votre programme :

```console
go run translate-text.go
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Exemple de réponse

Une réponse correcte est renvoyée au format JSON, comme dans l’exemple suivant :

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez des exemples Go pour les API Cognitive Services à partir de [Azure SDK pour Go](https://github.com/Azure/azure-sdk-for-go) sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des exemples Go sur GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Voir aussi

Apprenez à utiliser l’API de traduction de texte Translator Text pour :

* [Translittérer du texte](quickstart-go-transliterate.md)
* [Identifier la langue par entrée](quickstart-go-detect.md)
* [Obtenir des traductions alternatives](quickstart-go-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-go-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-go-sentences.md)
