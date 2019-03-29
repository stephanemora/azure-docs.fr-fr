---
title: 'Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalies et Java | Microsoft Docs'
description: Utiliser l’API de détecteur d’anomalie pour détecter les anomalies dans votre série de données en tant que lot ou diffusion en continu de données.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 06cb4d32359014f3cbc67ed1f75988c794e6599e
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619509"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalies et Java

Utilisez ce guide de démarrage rapide pour commencer à utiliser deux modes de détection de l’API de détecteur d’anomalie pour détecter les anomalies dans vos données de série chronologique. Cette application Java envoie deux demandes API contenant des données de série chronologique au format JSON et obtient les réponses.

| Demande d’API                                        | Sortie de l’application                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter les anomalies en tant que lot                        | La réponse JSON contenant l’état d’anomalie (et autres données) pour chaque point de données dans les données de série chronologique et les positions de toutes les anomalies détectées. |
| Détecter les anomalies le dernier point de données | La réponse JSON contenant l’état d’anomalie (et autres données) pour le dernier point de données dans les données de série chronologique.                                                                                                                                         |

 Bien que cette application soit écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Conditions préalables

- Le [Java&trade; développement Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure.

- Ces bibliothèques d’importation à partir du référentiel Maven
    - [JSON dans Java](https://mvnrepository.com/artifact/org.json/json) package
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) package

- Une JSON fichier conteneurs données chronologiques pointant. Vous trouverez l’exemple de données pour ce démarrage rapide sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Créer des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection des anomalies. Ils seront ajoutés à votre point de terminaison de service ultérieurement pour créer l’API URL de demande.

    |Méthode de détection  |URI  |
    |---------|---------|
    |Détection de lot    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Détection sur le dernier point de données     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Lire le fichier de données JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des demandes

1. Créer une nouvelle fonction appelée `sendRequest()` qui accepte les variables créées ci-dessus. Ensuite, effectuez les étapes suivantes.

2. Créer un `CloseableHttpClient` objet qui peut envoyer des demandes à l’API. Envoyer la demande à un `HttpPost` objet de requête en combinant votre point de terminaison et une URL de détecteur d’anomalies.

3. Utiliser la requête `setHeader()` fonction permettant de définir le `Content-Type` en-tête à `application/json`et ajoutez votre clé d’abonnement pour le `Ocp-Apim-Subscription-Key` en-tête.

4. Utilisez la requête `setEntity()` fonction aux données à envoyer.   

5. Utiliser le client `execute()` fonction pour envoyer la demande, puis enregistrez-le à un `CloseableHttpResponse` objet. 

6. Créer un `HttpEntity` objet pour stocker le contenu de la réponse. Obtenir le contenu avec `getEntity()`. Si la réponse n’est pas vide, renvoyez-le.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
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
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Détecter les anomalies en tant que lot

1. Créer une méthode appelée `detectAnomaliesBatch()` pour détecter les anomalies dans les données en tant que lot. Appelez le `sendRequest()` méthode créé ci-dessus avec votre point de terminaison, url, clé d’abonnement et les données json. Obtenir le résultat et l’imprimer à la console.

2. Trouver les positions des anomalies dans le jeu de données. La réponse `isAnomaly` champ contient une valeur booléenne relatives aux si un point de données donné est une anomalie. Obtenir le tableau JSON et effectuer une itération par son intermédiaire, l’impression de l’index de n’importe quel `true` valeurs. Ces valeurs correspondent à l’index de points de données anormales, si un a été trouvé.

    
    ```java
    static void detectAnomaliesBatch(String requestData) {
        System.out.println("Detecting anomalies as a batch");
        String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
        if (result != null) {
            System.out.println(result);
            JSONObject jsonObj = new JSONObject(result);
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
    ```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter les anomalies le dernier point de données

* Créer une méthode appelée `detectAnomaliesLatest()` pour détecter les anomalies du dernier point de données dans le jeu de données. Appelez le `sendRequest()` méthode créé ci-dessus avec votre point de terminaison, url, clé d’abonnement et les données json. Obtenir le résultat et l’imprimer à la console.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la demande

1. Dans la méthode principale de votre application, lire le fichier JSON contenant les données qui seront ajoutées aux requêtes.

2. Appelez les fonctions de détection de deux anomalie créées ci-dessus.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        detectAnomaliesBatch(requestData);
        detectAnomaliesLatest(requestData);
    }
    ```

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de réponse détection point plus récente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)