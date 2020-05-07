---
title: 'Démarrage rapide : Vérifier l’orthographe avec l’API REST et Java - Vérification orthographique Bing'
titleSuffix: Azure Cognitive Services
description: Commencez à utiliser l’API REST Vérification orthographique Bing pour vérifier l’orthographe et la grammaire.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 491481156f026e9887244064297d0790a965158e
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735111"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Démarrage rapide : Vérifier l’orthographe avec l’API REST Vérification orthographique Bing et Java

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API REST Vérification orthographique Bing. Cette simple application Java envoie une demande à l’API et retourne une liste de suggestions de corrections. Bien que cette application soit écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cette application est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Prérequis

* Kit de développement Java (JDK) 7 ou version ultérieure.

* Importez le fichier [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) ou la version [Gson](https://github.com/google/gson) la plus récente. Pour une exécution sur la ligne de commande, ajoutez `.jar` à votre dossier Java avec la classe main.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Créer et lancer une application

1. Créez un projet Java dans votre éditeur ou IDE favori avec un nom de classe de votre choix, puis importez les packages suivants.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Créez des variables pour l’hôte du point de terminaison d’API, le chemin et votre clé d’abonnement. Créez ensuite des variables pour votre marché, le texte dont vous souhaitez vérifier l’orthographe et une chaîne pour le mode de vérification orthographique. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Créer et envoyer une requête d’API

1. Créez une fonction appelée `check()` pour créer et envoyer la demande API. Dans celle-ci, effectuez les opérations suivantes. Créez une chaîne pour les paramètres de demande. Ajoutez le paramètre `?mkt=` à votre chaîne de marché et le paramètre `&mode=` à votre mode de vérification orthographique.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Créez une URL en combinant l’hôte du point de terminaison, le chemin et la chaîne de paramètres. Créez un objet `HttpsURLConnection`.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Ouvrez une connexion à l’URL. Définissez la méthode de demande sur `POST`. Ajoutez vos paramètres de demande. Veillez à ajouter votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Créez un objet `DataOutputStream` et envoyez la demande à l’API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Mettre en forme et lire la réponse de l’API

1. Ajoutez cette méthode à votre classe. Elle met en forme le code JSON pour obtenir une sortie plus lisible.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Créez un `BufferedReader` et lisez la réponse de l’API. Imprimez-la dans la console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Appeler l’API

Dans la fonction main de votre application, appelez la méthode check() créée ci-dessus.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>Exécution de l'application

Créez et exécutez votre projet.

Si vous utilisez la ligne de commande, utilisez les commandes suivantes pour générer et exécuter l’application.

**Générez :**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Exécutez :**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Exemple de réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorials/spellcheck.md)

- [Qu’est-ce que l’API Vérification orthographique Bing ?](../overview.md)
- [Informations de référence sur l’API Vérification orthographique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
