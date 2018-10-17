---
title: 'Démarrage rapide : API Recherche d’entités Bing, Java'
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
ms.openlocfilehash: 0cf1103b37572b43585e4a977dc4d034e7a50e2f
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814472"
---
# <a name="quickstart-for-bing-entity-search-api-with-java"></a>Démarrage rapide pour l’API Recherche d’entités Bing avec Java 

Cet article vous explique comment utiliser l’API [Recherche d’entités Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) avec Java.

## <a name="prerequisites"></a>Prérequis

Pour compiler et exécuter ce code, vous devez disposer de [JDK 7 ou 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Vous pouvez utiliser un IDE Java si vous le souhaitez, mais un éditeur de texte est suffisant.

Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec l’**API Recherche d’entités Bing**. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) est suffisant pour suivre ce guide de démarrage rapide. Vous aurez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou de la clé d’un abonnement payant présente sur votre tableau de bord Azure.

## <a name="search-entities"></a>Rechercher des entités

Pour exécuter cette application, suivez les étapes ci-dessous.

1. Créez un projet Java dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (EntitySearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac EntitySearch.java -cp .;gson-2.8.1.jar
 * java -cp .;gson-2.8.1.jar EntitySearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class EntitySearch {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/entities";

    static String mkt = "en-US";
    static String query = "italian restaurant near me";

    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);

        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify (String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = search ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
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
