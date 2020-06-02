---
title: 'Démarrage rapide : Envoyer une requête de recherche à l’API REST avec Python - Recherche d’entités Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour envoyer une requête à l’API REST Recherche d’entités Bing en Python et recevoir une réponse JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 43b440b631122f7057de68871f0a3f870588dc67
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650207"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Démarrage rapide : Envoyer une requête de recherche à l’API REST Recherche d’entités Bing en Python

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’entités Bing et voir la réponse JSON. Cette application simple en Python envoie une requête de recherche d’actualités à l’API, puis affiche la réponse. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

Bien que cette application soit écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Prérequis

* [Python](https://www.python.org/downloads/) 2.x ou 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis ajoutez les importations suivantes. Créez des variables pour votre clé d’abonnement, votre point de terminaison, votre marché et votre requête de recherche. Vous pouvez utiliser le point de terminaison global dans le code suivant, ou le point de terminaison de [sous-domaine personnalisé](../../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Créer une URL de requête en ajoutant la variable de votre marché au paramètre `?mkt=`. Encodez en URL votre requête et ajoutez-la au paramètre `&q=`. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Envoyer une requête et obtenir une réponse

1. Créez une fonction nommée `get_suggestions()`. 

2. Dans cette fonction, ajoutez votre clé d’abonnement à un dictionnaire avec `Ocp-Apim-Subscription-Key` comme clé.

3. Utilisez `http.client.HTTPSConnection()` pour créer un objet client HTTPS. Envoyez une requête `GET` en utilisant `request()` avec votre chemin et vos paramètres, ainsi que vos informations d’en-tête.

4. Stockez la réponse avec `getresponse()` et retournez `response.read()`.

   ```python
   def get_suggestions ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read()
   ```

5. Appelez `get_suggestions()` et affichez la réponse JSON.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
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

* [Qu’est-ce que l’API Recherche d’entités Bing ?](../search-the-web.md)
* [Informations de référence sur l’API Recherche d’entités Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
