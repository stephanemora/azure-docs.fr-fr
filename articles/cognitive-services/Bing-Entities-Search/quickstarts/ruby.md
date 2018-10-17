---
title: 'Démarrage rapide : API Recherche d’entités Bing, Ruby'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Recherche d’entités Bing.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 7e2b3fa520c17bb85fa04ca12bb762ce8f370371
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814455"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Démarrage rapide pour l’API Recherche d’entités Bing avec Ruby

Cet article vous explique comment utiliser l’API [Recherche d’entités Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) avec Ruby.

## <a name="prerequisites"></a>Prérequis

[Ruby 2.4](https://www.ruby-lang.org/en/downloads/) (ou une version ultérieure) est nécessaire pour exécuter ce code.

Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec l’**API Recherche d’entités Bing**. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) est suffisant pour suivre ce guide de démarrage rapide. Vous aurez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou de la clé d’un abonnement payant présente sur votre tableau de bord Azure.

## <a name="search-entities"></a>Rechercher des entités

Pour exécuter cette application, suivez les étapes ci-dessous.

1. Créez un nouveau projet Ruby dans votre environnement IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```ruby
require 'net/https'
require 'cgi'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsoft.com'
path = '/bing/v7.0/entities'

mkt = 'en-US'
query = 'italian restaurants near me'

params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
uri = URI (host + path + params)

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = subscriptionKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Réponse**

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
