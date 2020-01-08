---
title: 'Démarrage rapide : Envoyer une requête de recherche à l’API REST avec Node.js - Recherche d’entités Bing'
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
ms.openlocfilehash: f3585e96376a25721f478f9dd621835e75e3c600
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448628"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Démarrage rapide : Envoyer une requête de recherche à l’API REST Recherche d’entités Bing en utilisant Node.js

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’entités Bing et voir la réponse JSON. Cette application JavaScript simple envoie une requête de recherche d’actualités à l’API et affiche ensuite la réponse. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Alors que cette application est écrite en JavaScript, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Conditions préalables requises

* La dernière version de [Node.js](https://nodejs.org/en/download/).

* La [bibliothèque de requêtes JavaScript](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis définissez la sévérité et les exigences HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Créez des variables pour le point de terminaison d’API, votre clé d’abonnement et la requête de recherche. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Ajoutez votre marché et vos paramètres de requête à une chaîne appelée `query`. N’oubliez pas d’encoder par URL votre requête avec `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Gérer et analyser la réponse

1. Définissez une fonction nommée `response_handler` qui prend un appel HTTP, `response`, comme paramètre. Au sein de cette fonction, effectuez les étapes suivantes :

    1. Définissez une variable pour contenir le corps de la réponse JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Stockez le corps de la réponse quand l’indicateur **data** est appelé.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Quand un indicateur **end** est signalé, analysez le JSON, puis affichez-le.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Option Send a request (Envoyer une demande)

1. Créez une fonction appelée `Search` pour envoyer une requête de recherche. Dans cette requête, effectuez les étapes ci-après.

   1. Créez un objet JSON qui contient vos paramètres de requête : utilisez `Get` pour la méthode et ajoutez vos informations d’hôte et de chemin. Ajoutez votre clé d’abonnement dans l’en-tête `Ocp-Apim-Subscription-Key`. 
   2. Utilisez `https.request()` pour envoyer la requête avec le gestionnaire de réponse créé précédemment, ainsi que vos paramètres de recherche.
    
      ```javascript
      let Search = function () {
       let request_params = {
           method : 'GET',
           hostname : host,
           path : path + query,
           headers : {
               'Ocp-Apim-Subscription-Key' : subscriptionKey,
           }
       };
    
       let req = https.request (request_params, response_handler);
       req.end ();
      }
      ```

2. Appelez la fonction `Search()`.

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
