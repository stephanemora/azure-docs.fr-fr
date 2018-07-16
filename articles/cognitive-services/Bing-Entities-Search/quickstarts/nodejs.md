---
title: Démarrage rapide Node.js pour Azure Cognitive Services, API Recherche d'entités Bing | Microsoft Docs
description: Obtenez des informations et des exemples de code pour commencer rapidement à utiliser l’API Recherche d'entités Bing dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 7396976453c240d5ea9a767f8c26ac96d10d1e1f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370153"
---
# <a name="quickstart-for-microsoft-bing-entity-search-api-with-nodejs"></a>Démarrage rapide pour l’API Recherche d'entités Bing de Microsoft avec Node.js 
<a name="HOLTop"></a>

Cet article vous explique comment utiliser [l’API Recherche d'entités Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) avec Node.js.

## <a name="prerequisites"></a>Prérequis

Pour exécuter ce code, vous avez besoin de [Node.js 6](https://nodejs.org/en/download/).

Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec l’**API Recherche d'entités Bing**. Vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) pour suivre ce guide de démarrage rapide. Vous avez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou d’une clé d’abonnement payant de votre tableau de bord Azure.

## <a name="search-entities"></a>Rechercher des entités

Pour exécuter cette application, suivez les étapes ci-dessous.

1. Créez un projet Node.js dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```nodejs
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/entities';

let mkt = 'en-US';
let q = 'italian restaurant near me';

let params = '?mkt=' + mkt + '&q=' + encodeURI(q);

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.end ();
}

Search ();
```

**Réponse**

Une réponse correcte est renvoyée au format JSON, comme dans l’exemple suivant : 

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
        "url": "http://www.princi.com/",
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

[Revenir en haut](#HOLTop)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel sur la recherche d’entités Bing](../tutorial-bing-entities-search-single-page-app.md)
> [Vue d’ensemble de la recherche d’entités Bing](../search-the-web.md )
> [Référence de l’API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
