---
title: 'Démarrage rapide : Envoyer une requête de recherche à l’API REST avec C# - Recherche d’entités Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour envoyer une requête en C# à l’API REST Recherche d’entités Bing et recevoir une réponse JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: c343c160f67eda2dd390ffc39f3b4f1ff49cacb6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448665"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Démarrage rapide : Envoyer une requête de recherche à l’API REST Recherche d’entités Bing en C#

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’entités Bing et voir la réponse JSON. Cette application simple en C# envoie une requête de recherche d’actualités à l’API et affiche ensuite la réponse. Le code source de cette application est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Alors que cette application est écrite en C#, l’API est un service web RESTful compatible avec la plupart des langages de programmation.


## <a name="prerequisites"></a>Conditions préalables requises

- N’importe quelle édition de [Visual Studio 2017 ou ultérieure](https://www.visualstudio.com/downloads/).

- Le framework [Json.NET](https://www.newtonsoft.com/json), disponible sous forme de package NuGet. Pour installer le package NuGet dans Visual Studio :

   1. Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions**.
   2. Sélectionnez **Gérer les packages NuGet**.
   3. Recherchez *Newtonsoft.Json* et installez le package.

- Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Créer et initialiser un projet

1. Créez une solution console en C# dans Visual Studio. Ajoutez ensuite les espaces de noms suivants dans le fichier de code principal.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Créez une classe, puis ajoutez des variables pour le point de terminaison de l’API, votre clé d’abonnement et la requête que vous souhaitez rechercher. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Envoyer une requête et obtenir la réponse de l’API

1. Au sein de la classe, créez une fonction nommée `Search()`. Créez un objet `HttpClient` et ajoutez votre clé d’abonnement dans l’en-tête `Ocp-Apim-Subscription-Key`.

   1. Construisez l’URI de votre requête en combinant l’hôte et le chemin. Ajoutez ensuite votre marché et encodez par URL votre requête.
   2. Attendez `client.GetAsync()` pour obtenir une réponse HTTP, puis stockez la réponse json en attendant `ReadAsStringAsync()`.
   3. Mettez en forme la chaîne JSON avec `JsonConvert.DeserializeObject()` et imprimez-la sur la console.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

2. Dans la méthode principale de votre application, appelez la fonction `Search()`.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Exemple de réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorial-bing-entities-search-single-page-app.md)

* [Qu’est-ce que l’API Recherche d’entités Bing ?](../overview.md )
* [Informations de référence sur l’API Recherche d’entités Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
