---
title: 'Démarrage rapide : Vérifier l’orthographe avec l’API REST Vérification orthographique Bing et Java'
titlesuffix: Azure Cognitive Services
description: Commencez à utiliser l’API REST Vérification orthographique Bing pour vérifier l’orthographe et la grammaire.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d2905d05dce48b705de44780425ed2b55b02555c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888982"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Démarrage rapide : Vérifier l’orthographe avec l’API REST Vérification orthographique Bing et Java

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API REST Vérification orthographique Bing. Cette simple application Java envoie une demande à l’API et retourne une liste de suggestions de corrections. Bien que cette application soit écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cette application est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Prérequis

Kit de développement Java (JDK) 7 ou version ultérieure.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-an-application"></a>Créer et lancer une application

1. Créez un projet Java dans votre éditeur ou IDE favori, puis importez les packages suivants.

    ```java
    import java.io.*;
    import java.net.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Créez des variables pour l’hôte du point de terminaison d’API, le chemin et votre clé d’abonnement. Créez ensuite des variables pour votre marché, le texte dont vous souhaitez vérifier l’orthographe et une chaîne pour le mode de vérification orthographique.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "ENTER YOUR KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Créer et envoyer une requête d’API

1. Créez une fonction appelée `check()` pour créer et envoyer la demande API. Dans celle-ci, effectuez les opérations suivantes. Créez une chaîne pour les paramètres de demande. Ajoutez le paramètre `?mkt=` à votre chaîne de marché et le paramètre `&mode=` à votre mode de vérification orthographique.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
   //...
   }
   ```

2. Créez une URL en combinant l’hôte du point de terminaison, le chemin et la chaîne de paramètres. Créez un objet `HttpsURLConnection`.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) 
    ```

3. Ouvrez une connexion à l’URL. Définissez la méthode de demande sur `POST`. Ajoutez vos paramètres de demande. Veillez à ajouter votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`. 

    ```java
    url.openConnection();
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

## <a name="read-the-response"></a>Lire la réponse

1. Créez un `BufferedReader` et lisez la réponse de l’API. Imprimez-la dans la console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(line);
    }
    in.close();
    ```

2. Dans la fonction principale de votre application, appelez la fonction créée ci-dessus. 

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
- [Informations de référence sur l’API Vérification orthographique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
