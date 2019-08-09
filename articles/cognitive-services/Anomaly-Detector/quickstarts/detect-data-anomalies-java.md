---
title: 'Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et Java'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Détecteur d’anomalies pour détecter les anomalies dans vos séries de données, soit par lot, soit sur des données de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 001d53cbd7e2a57615ea3da71d128bd210a79921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565849"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et Java

Utilisez ce guide de démarrage rapide pour commencer à utiliser les deux modes de détection de l’API Détecteur d’anomalies afin de détecter les anomalies dans vos données de séries chronologiques. Cette application Java envoie deux requêtes d’API contenant des données de séries chronologiques au format JSON, et obtient les réponses.

| Requête d’API                                        | Sortie de l’application                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter des anomalies en tant que lot                        | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour chaque point de données dans les données de la série chronologique, et les positions des anomalies détectées. |
| Détecter l’état d’anomalie du dernier point de données | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour le dernier point de données dans les données de série chronologique.                                                                                                                                         |

 Bien que cette application soit écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Prérequis

- [Kit de développement Java&trade; (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure

- Importez ces bibliothèques à partir du référentiel Maven :
    - Package [JSON en Java](https://mvnrepository.com/artifact/org.json/json)
    - Package [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Fichier JSON contenant des points de données de séries chronologiques. Les exemples de données ce guide de démarrage rapide sont disponibles sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

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

2. Créez des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection d’anomalies. Ceux-ci seront ajoutés ultérieurement à votre point de terminaison de service pour créer les URL de requête de l’API.

    |Méthode de détection  |URI  |
    |---------|---------|
    |Détection par lot    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
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
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des requêtes

1. Créez une fonction appelée `sendRequest()` qui sélectionne les variables créées précédemment. Ensuite, effectuez les étapes suivantes.

2. Créez un objet `CloseableHttpClient` qui peut envoyer des requêtes à l’API. Envoyez la requête à un objet de requête `HttpPost` en combinant votre point de terminaison et une URL Détecteur d’anomalies.

3. Utilisez la fonction `setHeader()` de la requête pour affecter la valeur `application/json` à l’en-tête `Content-Type`, et ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

4. Utilisez la fonction `setEntity()` de la requête sur les données à envoyer.

5. Utilisez la fonction `execute()` du client pour envoyer la requête, et enregistrez-la dans un objet `CloseableHttpResponse`.

6. Créez un objet `HttpEntity` pour stocker le contenu de la réponse. Obtenez le contenu avec `getEntity()`. Si la réponse n’est pas vide, retournez-la.

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

## <a name="detect-anomalies-as-a-batch"></a>Détecter des anomalies par lot

1. Créez une méthode appelée `detectAnomaliesBatch()` pour détecter les anomalies dans le jeu de données sous forme de lot. Appelez la méthode `sendRequest()` créée ci-dessus avec votre point de terminaison, l’URL, la clé d’abonnement et les données JSON. Obtenez le résultat et imprimez-le dans la console.

2. Si la réponse contient un champ `code`, imprimez le code d’erreur et le message d’erreur.

3. Sinon, trouvez les positions des anomalies dans l’ensemble de données. Le champ `isAnomaly` de la réponse contient une valeur booléenne indiquant si un point de données particulier est une anomalie. Obtenez et effectuez une itération au sein du tableau JSON, en imprimant l’index des valeurs `true`. Ces valeurs correspondent à l’index des points de données anormaux, le cas échéant.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

* Créez une méthode appelée `detectAnomaliesLatest()` pour détecter l’état d’anomalie du dernier point de données dans le jeu de données. Appelez la méthode `sendRequest()` créée ci-dessus avec votre point de terminaison, l’URL, la clé d’abonnement et les données JSON. Obtenez le résultat et imprimez-le dans la console.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la requête

1. Dans la méthode Main de votre application, lisez le fichier JSON contenant les données qui seront ajoutées aux requêtes.

2. Appelez les deux fonctions de détection d’anomalie créées plus haut.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de dernière réponse de détection de points](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
