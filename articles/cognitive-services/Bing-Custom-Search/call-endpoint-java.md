---
title: 'Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Java | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing, en Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 6a470979ce4276d510f1a89bc9bc192d609c2083
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973507"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Java

Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing. Si d’un côté cette application est écrite en Java, de l’autre l’API Recherche personnalisée Bing constitue un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Prérequis

- Une instance Recherche personnalisée Bing. Consultez [Démarrage rapide : Créer votre première instance Recherche personnalisée Bing](quick-start.md) pour plus amples informations.

- La version la plus récente de [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- La [bibliothèque Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un projet Java dans votre éditeur ou IDE favori, puis importez les bibliothèques suivantes.

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Créez une classe nommée `CustomSrchJava` et créez des variables pour votre clé d’abonnement, le point de terminaison de recherche personnalisée et l’ID de configuration personnalisée de votre instance de recherche. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Créez une autre classe nommée `SearchResults` qui recevra réponse de votre instance Recherche personnalisée Bing.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Créez une fonction nommée `prettify()` pour mettre en forme la réponse JSON à partir de l’API Recherche personnalisée Bing.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Envoyer et recevoir une requête de recherche 

1. Créez une fonction nommée `SearchWeb()` qui envoie une requête et renvoie un objet `SearchResults`. Créez l’URL de la requête en combinant l’ID de votre configuration personnalisée, la requête et les informations de point de terminaison. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Créez un flux de données et stockez la réponse JSON dans un objet `SearchResults`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. Dans la méthode principale de votre application, appelez `SearchWeb()` avec votre terme de recherche, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Exécutez le programme.
    
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Recherche personnalisée](./tutorials/custom-search-web-page.md)
