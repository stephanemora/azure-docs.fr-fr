---
title: 'Démarrage rapide : Project Answer Search, Java'
titlesuffix: Azure Cognitive Services
description: Commencez à utiliser Project Answer Search dans Java.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: e28b86981eafdf353f4a0e1937e5731aa4e7d68e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707132"
---
# <a name="quickstart-project-answer-search-query-in-java"></a>Démarrage rapide : Requête Project Answer Search avec Java
Cet article utilise Java pour illustrer l’API Bing Answer Search, qui fait partie de Microsoft Cognitive Services sur Azure. L’API est un service web REST compatible avec n’importe quel langage de programmation capable de formuler des requêtes HTTP et d’analyser JSON.
 
L’exemple de code utilise Java avec des dépendances externes minimales.  Vous pouvez également l’exécuter sur Linux ou Mac OS X à l’aide de Mono.

## <a name="prerequisites"></a>Prérequis

Obtenir une clé d’accès pour l’essai gratuit [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

## <a name="request"></a>Requête 

Le code suivant crée un `WebRequest`, définit l’en-tête de clé d’accès et ajoute une chaîne de requête pour « Gibraltar ».  Ensuite, il envoie la requête et affecte la réponse à une chaîne contenant le texte JSON.

```
    static String host = "https://api.labs.cognitive.microsoft.com";
    static String path = "/answerSearch/v7.0/search";

    // construct URL of search request (endpoint + query string)

    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="complete-code"></a>Code complet

L’API Bing Answer Search retourne des résultats à partir du moteur de recherche Bing.
1. Téléchargez ou installez la bibliothèque gson.
2. Créez un projet Java dans votre éditeur ou IDE favori.
3. Ajoutez le code ci-dessous.
4. Remplacez la valeur subscriptionKey par une clé d’accès valide pour votre abonnement.
5. Exécutez le programme.

```
package knowledgeAPI;
import java.io.InputStream;
import java.net.*;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class KnowledgeSrch {

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.labs.cognitive.microsoft.com";
        static String path = "/answerSearch/v7.0/search";

        static String searchTerm = "Gibraltar";

        public static SearchResults SearchKnowledge (String searchQuery) throws Exception {

            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
            
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchKnowledge(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
}

```

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](c-sharp-quickstart.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide de Node](node-quickstart.md)
