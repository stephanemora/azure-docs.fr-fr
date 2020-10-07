---
title: 'Démarrage rapide : Analyser une image distante avec l’API REST et Java'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous analysez une image en utilisant l’API Vision par ordinateur avec Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5d65704b0d7b0345b4ff5d952a0f7f28fad0ea44
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87835151"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-java"></a>Démarrage rapide : Analyser une image distante à l’aide de l’API REST Vision par ordinateur et Java

Dans ce guide de démarrage rapide, vous allez analyser une image stockée à distance pour extraire des caractéristiques visuelles à l’aide de Java et de l’API REST Vision par ordinateur. Avec la [méthode d’analyse d’une image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), vous pouvez extraire des fonctionnalités visuelles en fonction du contenu de l’image.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Plateforme [Java&trade;, Standard Edition Development Kit 7 ou 8](https://aka.ms/azure-jdks) (JDK 7 ou 8)
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL du point de terminaison, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>Créer et exécuter l’exemple d’application

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Créez un projet Java dans votre éditeur ou IDE favori. Si l’option est disponible, créez le projet Java à partir d’un modèle d’application de ligne de commande.
1. Importez les bibliothèques suivantes dans votre projet Java. Si vous utilisez Maven, les coordonnées Maven sont fournies pour chaque bibliothèque.
   - [Client HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Noyau HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Bibliothèque JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Ajoutez les instructions `import` suivantes au fichier qui contient la classe publique `Main` pour votre projet.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Remplacez la classe publique `AnalyzeImage` par le code suivant.
1. Le cas échéant, remplacez la valeur de `imageToAnalyze` par l’URL d’une image différente que vous souhaitez analyser.

```java
public class AnalyzeImage {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + "vision/v3.0/analyze";
    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
            "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="compile-and-run-the-program"></a>Compiler et exécuter le programme

1. Enregistrez les modifications, puis générez le projet Java.
1. Si vous utilisez un IDE, exécutez `Main`.

Ou bien, si vous exécutez le programme à partir d’une fenêtre de ligne de commande, exécutez les commandes suivantes. Ces commandes supposent que vos bibliothèques se trouvent dans un dossier nommé `libs` qui figure dans le même dossier que `Main.java` ; si ce n’est pas le cas, vous devez remplacer `libs` par le chemin de vos bibliothèques.

1. Compilez le fichier `Main.java`.

    ```bash
    javac -cp ".;libs/*" Main.java
    ```

1. Exécutez le programme. Il envoie la requête à l’API QnA Maker pour créer la base de connaissances, puis demande des résultats toutes les 30 secondes. Chaque réponse est affichée dans la fenêtre de ligne de commande.

    ```bash
    java -cp ".;libs/*" Main
    ```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple d’application analyse et affiche une réponse réussie dans la fenêtre de console, comme dans l’exemple suivant :

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Java Swing qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent, ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Didacticiel sur l’API Vision par ordinateur avec Java](../Tutorials/java-tutorial.md)
