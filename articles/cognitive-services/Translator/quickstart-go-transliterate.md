---
title: 'Démarrage rapide : Convertir un script de texte, Go - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez convertir le texte d’un script d’une langue à une autre à l’aide de l’API de traduction de texte Translator Text avec Go.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: fe8b1807ea08529ad6be11493de659bd32f0a129
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57858216"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-go"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text pour translittérer du texte à l’aide de Go

Dans ce démarrage rapide, vous convertissez du texte dans la langue d’un script vers une autre à l’aide de l’API de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Go](https://golang.org/doc/install)
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Go dans votre IDE ou éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `transliterate-text.go`.

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
     * This calls our transliterate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    transliterate(subscriptionKey)
}
```

## <a name="create-a-function-to-transliterate-text"></a>Créer une fonction pour translittérer du texte

Créons une fonction pour translittérer du texte. Cette fonction n’utilisera qu’un seul argument, votre clé d’abonnement Translator Text.

```go
func transliterate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Passons ensuite à la génération de l’URL. L’URL est générée à l’aide des méthodes `Parse()` et `Query()`. Vous remarquerez que des paramètres sont ajoutés avec la méthode `Add()`. Dans cet exemple, nous allons translittérer le japonais pour utiliser l’alphabet latin.

Copiez ce code dans la fonction `transliterate`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0")
q := u.Query()
q.Add("language", "ja")
q.Add("fromScript", "jpan")
q.Add("toScript", "latn")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Pour plus d’informations sur les points de terminaison, les routes et les paramètres de requête, consultez [API de traduction de texte Translator Text 3.0 : Translittérer](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

## <a name="create-a-struct-for-your-request-body"></a>Créer une structure pour le corps de votre demande

Créez ensuite une structure anonyme pour le corps de la demande et codez-la au format JSON avec `json.Marshal()`. Ajoutez ce code à la fonction `transliterate`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "こんにちは"},
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

Ajoutez ce code à la fonction `transliterate` pour décoder la réponse JSON, puis mettez en forme et imprimez le résultat.

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
go run transliterate-text.go
```

Si vous souhaitez comparer votre code avec le nôtre, l’exemple de code complet est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Exemple de réponse

```json
[
  {
    "script": "latn",
    "text": "konnichiwa"
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez des packages Go pour les API Cognitive Services à partir de [Azure SDK pour Go](https://github.com/Azure/azure-sdk-for-go) sur GitHub.

> [!div class="nextstepaction"]
> [Explorer des packages Go sur GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Voir aussi

Apprenez à utiliser l’API de traduction de texte Translator Text pour :

* [Traduire le texte](quickstart-go-translate.md)
* [Identifier la langue par entrée](quickstart-go-detect.md)
* [Obtenir des traductions alternatives](quickstart-go-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-go-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-go-sentences.md)
