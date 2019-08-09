---
title: 'Démarrage rapide : Reconnaître l’encre numérique avec l’API REST Ink Recognizer et Java'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Ink Recognizer pour démarrer la reconnaissance des traits d’encre numérique.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: b1c739b6355d3b32063e5289720ed1d191611e65
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721230"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Démarrage rapide : Reconnaître l’encre numérique avec l’API REST Ink Recognizer et Java

Utilisez ce guide de démarrage rapide pour commencer à appliquer l’API Ink Recognizer aux traits d’encre numérique. Cette application Java envoie une requête d’API contenant des données de trait d’encre au format JSON, puis obtient la réponse.

Bien que cette application soit écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

En règle générale, vous appelez l’API à partir d’une application d’écriture manuscrite numérique. Ce guide de démarrage rapide envoie des données de trait d’encre pour l’exemple d’écriture manuscrite suivant à partir d’un fichier JSON.

![une image de texte manuscrit](../media/handwriting-sample.jpg)

Le code source de ce guide de démarrage rapide est disponible sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Prérequis

- [Kit de développement Java&trade; (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure

- Importez ces bibliothèques à partir du référentiel Maven :
    - Package [JSON en Java](https://mvnrepository.com/artifact/org.json/json)
    - Package [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Vous trouverez l’exemple de données de trait d’encre utilisé dans ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Créez un projet Java dans votre éditeur ou IDE favori, puis importez les bibliothèques suivantes.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Créez des variables pour votre clé d’abonnement et votre point de terminaison. Vous trouverez ci-dessous l’URI que vous pouvez utiliser pour la reconnaissance de l’écriture manuscrite. Il sera ajouté ultérieurement à votre point de terminaison de service pour créer l’URL de requête d’API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des requêtes

1. Créez une fonction appelée `sendRequest()` qui sélectionne les variables créées précédemment. Ensuite, effectuez les étapes suivantes.

2. Créez un objet `CloseableHttpClient` qui peut envoyer des requêtes à l’API. Envoyez la requête à un objet de requête `HttpPut` en combinant votre point de terminaison et l’URL Ink Recognizer.

3. Utilisez la fonction `setHeader()` de la requête pour affecter la valeur `application/json` à l’en-tête `Content-Type`, et ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

4. Utilisez la fonction `setEntity()` de la requête sur les données à envoyer.   

5. Utilisez la fonction `execute()` du client pour envoyer la requête, et enregistrez-la dans un objet `CloseableHttpResponse`. 

6. Créez un objet `HttpEntity` pour stocker le contenu de la réponse. Obtenez le contenu avec `getEntity()`. Si la réponse n’est pas vide, retournez-la.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.setEntity(new StringEntity(requestData));
            try (CloseableHttpResponse response = client.execute(request)) {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    return EntityUtils.toString(respEntity, "utf-8");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            }
        } catch (IOException ex) {
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Envoyer une requête de reconnaissance de l’écriture manuscrite

Créez une méthode appelée `recognizeInk()` pour reconnaître vos données de trait d’encre. Appelez la méthode `sendRequest()` créée ci-dessus avec votre point de terminaison, l’URL, la clé d’abonnement et les données JSON. Obtenez le résultat et imprimez-le dans la console.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Charger vos données d’encre numérique et envoyer la requête

1. Dans la méthode Main de votre application, lisez le fichier JSON contenant les données qui seront ajoutées aux requêtes.

2. Appelez la fonction de reconnaissance de l’écriture manuscrite créée ci-dessus.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Exécuter l’application et afficher la réponse

Exécutez l'application. Une réponse correcte est retournée au format JSON. Vous pouvez également trouver la réponse JSON sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Pour observer le fonctionnement de l’API Ink Recognizer dans une application d’écriture manuscrite numérique, examinez les exemples d’applications suivants sur GitHub :
* [C# et UWP (plateforme Windows universelle)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# et WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Application de navigateur web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Application mobile Android et Java](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Application mobile Swift et iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
