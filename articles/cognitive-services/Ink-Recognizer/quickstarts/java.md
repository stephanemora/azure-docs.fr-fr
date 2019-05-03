---
title: 'Démarrage rapide : Reconnaître l’encre numérique avec l’API REST de module de reconnaissance d’encre et Java'
description: Utilisez l’API de module de reconnaissance de l’encre pour démarrer la reconnaissance des traits d’encre numérique.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 43dc388c9f24e3975d6efddbeec6e096bf062548
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026272"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Démarrage rapide : Reconnaître l’encre numérique avec l’API REST de module de reconnaissance d’encre et Java

Utilisez ce guide de démarrage rapide pour commencer à utiliser l’API de module de reconnaissance de l’encre sur traits d’encre numérique. Cette application Java envoie une demande d’API contenant des données de trait d’encre au format JSON et obtient la réponse.

Bien que cette application soit écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

En règle générale, vous appelez l’API à partir d’une application pour l’écriture manuscrite numérique. Ce démarrage rapide envoie des données de trait d’encre pour l’exemple suivant manuscrite à partir d’un fichier JSON.

![une image de texte manuscrit](../media/handwriting-sample.jpg)

Le code source de ce guide de démarrage rapide est disponible sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Conditions préalables

- Le [Java&trade; développement Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure.

- Ces bibliothèques d’importation à partir du référentiel Maven
    - [JSON dans Java](https://mvnrepository.com/artifact/org.json/json) package
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) package

- Vous trouverez l’exemple de données de trait d’encre pour ce démarrage rapide sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

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

2. Créer des variables pour votre clé d’abonnement et votre point de terminaison. Voici l’URI que vous pouvez utiliser pour la reconnaissance de l’encre. Il sera ajouté à votre point de terminaison de service ultérieurement pour créer l’URL de demande d’API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des demandes

1. Créer une nouvelle fonction appelée `sendRequest()` qui accepte les variables créées ci-dessus. Ensuite, effectuez les étapes suivantes.

2. Créer un `CloseableHttpClient` objet qui peut envoyer des demandes à l’API. Envoyer la demande à un `HttpPut` objet de requête en combinant votre point de terminaison et l’URL de module de reconnaissance de l’encre.

3. Utiliser la requête `setHeader()` fonction permettant de définir le `Content-Type` en-tête à `application/json`et ajoutez votre clé d’abonnement pour le `Ocp-Apim-Subscription-Key` en-tête.

4. Utilisez la requête `setEntity()` fonction aux données à envoyer.   

5. Utiliser le client `execute()` fonction pour envoyer la demande, puis enregistrez-le à un `CloseableHttpResponse` objet. 

6. Créer un `HttpEntity` objet pour stocker le contenu de la réponse. Obtenir le contenu avec `getEntity()`. Si la réponse n’est pas vide, renvoyez-le.
    
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

## <a name="send-an-ink-recognition-request"></a>Envoyer une demande de reconnaissance de l’encre

Créer une méthode appelée `recognizeInk()` pour reconnaître vos données de trait d’encre. Appelez le `sendRequest()` méthode créé ci-dessus avec votre point de terminaison, url, clé d’abonnement et les données json. Obtenir le résultat et l’imprimer à la console.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Chargez vos données de l’encre numérique et envoyer la demande

1. Dans la méthode principale de votre application, lire le fichier JSON contenant les données qui seront ajoutées aux requêtes.

2. Appelez la fonction de reconnaissance de l’encre créée ci-dessus.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Exécutez l’application et afficher la réponse

Exécutez l'application. Une réponse correcte est retournée au format JSON. Vous pouvez également trouver la réponse JSON sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Pour observer le fonctionnement de l’API de reconnaissance de l’encre dans une application pour l’écriture manuscrite numérique, examinez les exemples d’applications suivants sur GitHub :
* [C#et Windows Universal Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C#et Foundation (WPF) de Windows Presentation](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Application de navigateur web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Application mobile Java et Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Application mobile SWIFT et iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
