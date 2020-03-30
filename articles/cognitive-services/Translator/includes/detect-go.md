---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: d75c925ef55163ce06b2ceff585e230d95b38c77
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71837504"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Go dans votre IDE ou éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `detect-language.go`.

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

Cet exemple tente de lire la clé d’abonnement et le point de terminaison Translator Text depuis ces variables d’environnement : `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` et `TRANSLATOR_TEXT_ENDPOINT`. Si vous n’êtes pas familiarisé avec les variables d’environnement, vous pouvez définir `subscriptionKey` et `endpoint` en tant que chaînes et commenter les instructions conditionnelles.

Copiez ce code dans votre projet :

```go
func main() {
    /*
    * Read your subscription key from an env variable.
    * Please note: You can replace this code block with
    * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
    * want to use env variables. If so, be sure to delete the "os" import.
    */
    if "" == os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_SUBSCRIPTION_KEY.")
    }
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY")
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/detect?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    detect(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-detect-the-text-language"></a>Créer une fonction pour détecter la langue du texte

Créons une fonction pour détecter la langue du texte. Cette fonction n’utilisera qu’un seul argument, votre clé d’abonnement d’API de traduction de texte Translator Text.

```go
func detect(subscriptionKey string, uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Passons ensuite à la génération de l’URL. L’URL est générée à l’aide des méthodes `Parse()` et `Query()`.

Copiez ce code dans la fonction `detect`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> Pour plus d’informations concernant les points de terminaison, les itinéraires et les paramètres de la requête, consultez [API de traduction de texte Translator Text 3.0 : Détecter](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

## <a name="create-a-struct-for-your-request-body"></a>Créer une structure pour le corps de la demande

Créez ensuite une structure anonyme pour le corps de la demande et codez-la au format JSON avec `json.Marshal()`. Ajoutez ce code à la fonction `detect`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Salve, Mondo!"},
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

Si vous utilisez un abonnement multiservice Cognitive Services, vous devez également inclure la `Ocp-Apim-Subscription-Region` dans vos paramètres de requête. [En savoir plus sur l’authentification sur l’abonnement multiservice](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Gérer et imprimer la réponse

Ajoutez ce code à la fonction `detect` pour décoder la réponse JSON, puis mettez en forme et imprimez le résultat.

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
go run detect-language.go
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Exemple de réponse

Après avoir exécuté l’exemple, vous devriez voir ce qui suit sur le terminal :

> [!NOTE]
> Recherchez l’abréviation du pays/de la région dans cette [liste de langues](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).


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

Consultez les informations de référence sur l’API pour comprendre tout ce que vous pouvez faire avec l’API Traduction de texte Translator Text.

> [!div class="nextstepaction"]
> [Informations de référence sur l'API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
