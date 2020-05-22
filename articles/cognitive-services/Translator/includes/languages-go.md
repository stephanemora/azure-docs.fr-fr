---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: edde734409d064f1bff0212a1cbaecf136206772
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586855"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Go avec votre IDE ou votre éditeur favori, ou créez un dossier sur votre poste de travail. Copiez ensuite cet extrait de code dans votre projet/dossier, dans un fichier nommé `get-languages.go`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Créer la fonction main

Nous allons créer la fonction principale de notre application. Notez qu’il s’agit d’une seule ligne de code. C’est parce que nous créons une seule fonction pour obtenir et imprimer la liste des langues prises en charge par Translator.

Cet exemple tente de lire le point de terminaison Translator depuis une variable d’environnement : `TRANSLATOR_TEXT_ENDPOINT`. Si vous n’êtes pas familiarisé avec les variables d’environnement, vous pouvez définir `endpoint` en tant que chaîne et commentez l’instruction conditionnelle.

Copiez ce code dans votre projet :

```go
func main() {
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/languages?api-version=3.0"
    getLanguages(uri)
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>Créer une fonction pour obtenir la liste des langues prises en charge

Créons une fonction pour obtenir la liste des langues prises en charge.

```go
func getLanguages(uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Passons ensuite à la génération de l’URL. L’URL est générée à l’aide des méthodes `Parse()` et `Query()`.

Copiez ce code dans la fonction `getLanguages`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> Pour plus d’informations sur les points de terminaison, les itinéraires et les paramètres de requête, consultez [Translator 3.0 : Langues](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

## <a name="build-the-request"></a>Générer la demande

Maintenant que vous avez codé le corps de la demande au format JSON, vous pouvez créer votre requête POST et appeler Translator.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Si vous utilisez un abonnement multiservice Cognitive Services, vous devez également inclure la `Ocp-Apim-Subscription-Region` dans vos paramètres de requête. [En savoir plus sur l’authentification sur l’abonnement multiservice](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Gérer et imprimer la réponse

Ajoutez ce code à la fonction `getLanguages` pour décoder la réponse JSON, puis mettez en forme et imprimez le résultat.

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

Voilà, vous avez installé un programme simple qui appellera Translator et enverra une réponse JSON. Il est maintenant temps d’exécuter votre programme :

```console
go run get-languages.go
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Exemple de réponse

Recherchez l’abréviation du pays/de la région dans cette [liste de langues](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Une réponse correcte est renvoyée au format JSON, comme dans l’exemple suivant :

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les informations de référence sur l’API pour comprendre tout ce que vous pouvez faire avec Translator.

> [!div class="nextstepaction"]
> [Informations de référence sur l'API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
