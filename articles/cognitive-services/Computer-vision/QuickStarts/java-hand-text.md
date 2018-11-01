---
title: 'Démarrage rapide : Extraire du texte manuscrit - REST, Java - Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous extrayez le texte manuscrit d’une image en utilisant l’API Vision par ordinateur avec Java.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: f3600c5cf64614bcb5600b664147c9f5abb3865b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417254"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-java-in-computer-vision"></a>Démarrage rapide : Extraire du texte manuscrit à l’aide de l’API REST et Java dans Vision par ordinateur

Dans ce guide de démarrage rapide, vous extrayez le texte manuscrit d’une image à l’aide de l’API REST de Vision par ordinateur. Avec les méthodes [Reconnaître le texte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) et [Obtenir le résultat de l’opération Reconnaître le texte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201), vous pouvez détecter le texte manuscrit dans une image, puis extraire les caractères reconnus dans un flux de caractères exploitable automatiquement.

> [!IMPORTANT]
> Contrairement à la méthode [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), la méthode [Reconnaître le texte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) s’exécute de façon asynchrone. Cette méthode ne retourne pas d’informations dans le corps d’une réponse réussie. À la place, la méthode Reconnaître le texte retourne un URI dans la valeur du champ d’en-tête de réponse `Operation-Content`. Vous pouvez ensuite appeler cet URI, qui représente la méthode [Obtenir le résultat de l’opération Reconnaître le texte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201), pour vérifier l’état de l’appel de la méthode Reconnaître le texte et retourner les résultats.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Vous devez utiliser la plateforme [Java&trade;, avec le kit Standard Edition Development 7 ou 8](https://aka.ms/azure-jdks) (JDK 7 ou 8) installé.
- Vous devez avoir une clé d’abonnement pour Vision par ordinateur. Pour obtenir une clé d’abonnement, consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

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
   import org.apache.http.client.methods.HttpGet;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.apache.http.Header;
   import org.json.JSONObject;
   ```

1. Remplacez la classe publique `Main` par le code suivant et, au besoin, effectuez les modifications suivantes dans le code :
   1. Remplacez la valeur de `subscriptionKey` par votre clé d’abonnement.
   1. Si nécessaire, remplacez la valeur de `uriBase` par l’URL du point de terminaison de la méthode [Reconnaître le texte](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) dans la région Azure où vous avez obtenu vos clés d’abonnement.
   1. Remplacez éventuellement la valeur de `imageToAnalyze` par l’URL d’une autre image à partir de laquelle vous voulez extraire le texte manuscrit.
1. Enregistrez les modifications, puis générez le projet Java.
1. Si vous utilisez un IDE, exécutez `Main`. Sinon, ouvrez une fenêtre d’invite de commandes et utilisez la commande `java` pour exécuter la classe compilée. Par exemple : `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the West Central US region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/recognizeText";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
        "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpTextClient = HttpClientBuilder.create().build();
        CloseableHttpClient httpResultClient = HttpClientBuilder.create().build();;

        try {
            // This operation requires two REST API calls. One to submit the image
            // for processing, the other to retrieve the text found in the image.

            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameter.
            builder.setParameter("mode", "Handwritten");

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

            // Two REST API methods are required to extract handwritten text.
            // One method to submit the image for processing, the other method
            // to retrieve the text found in the image.

            // Call the first REST API method to detect the text.
            HttpResponse response = httpTextClient.execute(request);

            // Check for success.
            if (response.getStatusLine().getStatusCode() != 202) {
                // Format and display the JSON error message.
                HttpEntity entity = response.getEntity();
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
                return;
            }

            // Store the URI of the second REST API method.
            // This URI is where you can get the results of the first REST API method.
            String operationLocation = null;

            // The 'Operation-Location' response header value contains the URI for
            // the second REST API method.
            Header[] responseHeaders = response.getAllHeaders();
            for (Header header : responseHeaders) {
                if (header.getName().equals("Operation-Location")) {
                    operationLocation = header.getValue();
                    break;
                }
            }

            if (operationLocation == null) {
                System.out.println("\nError retrieving Operation-Location.\nExiting.");
                System.exit(1);
            }

            // If the first REST API method completes successfully, the second
            // REST API method retrieves the text written in the image.
            //
            // Note: The response may not be immediately available. Handwriting
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the handwritten text.
            // You may need to wait or retry this operation.

            System.out.println("\nHandwritten text submitted.\n" +
                    "Waiting 10 seconds to retrieve the recognized text.\n");
            Thread.sleep(10000);

            // Call the second REST API method and get the response.
            HttpGet resultRequest = new HttpGet(operationLocation);
            resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            HttpResponse resultResponse = httpResultClient.execute(resultRequest);
            HttpEntity responseEntity = resultResponse.getEntity();

            if (responseEntity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(responseEntity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Text recognition result response: \n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple d’application analyse et affiche une réponse réussie dans la fenêtre de console, comme dans l’exemple suivant :

```json
Handwritten text submitted. Waiting 10 seconds to retrieve the recognized text.

Text recognition result response:

{
  "status": "Succeeded",
  "recognitionResult": {"lines": [
    {
      "boundingBox": [
        2,
        84,
        783,
        96,
        782,
        154,
        1,
        148
      ],
      "words": [
        {
          "boundingBox": [
            6,
            86,
            92,
            87,
            71,
            151,
            0,
            150
          ],
          "text": "Pack"
        },
        {
          "boundingBox": [
            86,
            87,
            172,
            88,
            150,
            152,
            64,
            151
          ],
          "text": "my"
        },
        {
          "boundingBox": [
            165,
            88,
            241,
            89,
            219,
            152,
            144,
            152
          ],
          "text": "box"
        },
        {
          "boundingBox": [
            234,
            89,
            343,
            90,
            322,
            154,
            213,
            152
          ],
          "text": "with"
        },
        {
          "boundingBox": [
            347,
            90,
            432,
            91,
            411,
            154,
            325,
            154
          ],
          "text": "five"
        },
        {
          "boundingBox": [
            432,
            91,
            538,
            92,
            516,
            154,
            411,
            154
          ],
          "text": "dozen"
        },
        {
          "boundingBox": [
            554,
            92,
            696,
            94,
            675,
            154,
            533,
            154
          ],
          "text": "liquor"
        },
        {
          "boundingBox": [
            710,
            94,
            800,
            96,
            800,
            154,
            688,
            154
          ],
          "text": "jugs"
        }
      ],
      "text": "Pack my box with five dozen liquor jugs"
    },
    {
      "boundingBox": [
        2,
        52,
        65,
        46,
        69,
        89,
        7,
        95
      ],
      "words": [{
        "boundingBox": [
          0,
          62,
          79,
          39,
          94,
          82,
          0,
          105
        ],
        "text": "dog"
      }],
      "text": "dog"
    },
    {
      "boundingBox": [
        6,
        2,
        771,
        13,
        770,
        75,
        5,
        64
      ],
      "words": [
        {
          "boundingBox": [
            8,
            4,
            92,
            5,
            77,
            71,
            0,
            71
          ],
          "text": "The"
        },
        {
          "boundingBox": [
            89,
            5,
            188,
            5,
            173,
            72,
            74,
            71
          ],
          "text": "quick"
        },
        {
          "boundingBox": [
            188,
            5,
            323,
            6,
            308,
            73,
            173,
            72
          ],
          "text": "brown"
        },
        {
          "boundingBox": [
            316,
            6,
            386,
            6,
            371,
            73,
            302,
            73
          ],
          "text": "fox"
        },
        {
          "boundingBox": [
            396,
            7,
            508,
            7,
            493,
            74,
            381,
            73
          ],
          "text": "jumps"
        },
        {
          "boundingBox": [
            501,
            7,
            604,
            8,
            589,
            75,
            487,
            74
          ],
          "text": "over"
        },
        {
          "boundingBox": [
            600,
            8,
            673,
            8,
            658,
            75,
            586,
            75
          ],
          "text": "the"
        },
        {
          "boundingBox": [
            670,
            8,
            800,
            9,
            787,
            76,
            655,
            75
          ],
          "text": "lazy"
        }
      ],
      "text": "The quick brown fox jumps over the lazy"
    }
  ]}
}
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez le projet Java, y compris la classe compilée et les bibliothèques importées.

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Java Swing qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent, ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Didacticiel sur l’API Vision par ordinateur avec Java](../Tutorials/java-tutorial.md)
