---
title: 'Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalie et C# | Microsoft Docs'
description: Utiliser l’API de détecteur d’anomalie pour détecter les anomalies dans votre série de données en tant que lot ou diffusion en continu de données.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: ffa42eb2247d163dd6a146ffb26c1f8cc5cf6d82
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922522"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalie etC# 

Utilisez ce guide de démarrage rapide pour commencer à utiliser deux modes de détection de l’API de détecteur d’anomalie pour détecter les anomalies dans vos données de série chronologique. Cela C# application envoie deux demandes API contenant des données de série chronologique au format JSON et obtient les réponses.

| Demande d’API                                        | Sortie de l’application                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter les anomalies en tant que lot                        | La réponse JSON contenant l’état d’anomalie (et autres données) pour chaque point de données dans les données de série chronologique et les positions de toutes les anomalies détectées. |
| Détecter les anomalies le dernier point de données | La réponse JSON contenant l’état d’anomalie (et autres données) pour le dernier point de données dans les données de série chronologique.                                                                                                                                         |

 Bien que cette application soit écrite en C#, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Conditions préalables

- N’importe quelle édition de [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Pour installer Newtonsoft.Json sous forme de package NuGet dans Visual studio :
        1. Cliquez avec le bouton droit sur le **Manager de la Solution**
        2. Cliquez sur **Gérer les packages NuGet...**
        3. Recherchez `Newtonsoft.Json` et installez le package
- Si vous utilisez Linux/Mac OS, cette application peut être exécuté à l’aide de [Mono](https://www.mono-project.com/).

- Une JSON fichier conteneurs données chronologiques pointant. Vous trouverez l’exemple de données pour ce démarrage rapide sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Dans Visual Studio, créez une nouvelle solution de console et ajoutez les packages suivants. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Créer des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection des anomalies. Ils seront ajoutés à votre point de terminaison de service ultérieurement pour créer l’API URL de demande.

    |Méthode de détection  |URI  |
    |---------|---------|
    |Détection de lot    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Détection sur le dernier point de données     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des demandes

1. Créez une fonction async appelée `Request` qui accepte les variables créées ci-dessus.

2. Définir le protocole de sécurité et informations d’en-tête à l’aide du client un `HttpClient` objet. Veillez à ajouter votre clé d’abonnement pour le `Ocp-Apim-Subscription-Key` en-tête. Créez ensuite un `StringContent` objet pour la requête.

3. Envoyer la demande avec `PostAsync()`, puis retourner la réponse.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Détecter les anomalies en tant que lot

1. Créer une nouvelle fonction appelée `detectAnomaliesBatch()`. Construire la demande et l’envoyer en appelant le `Request()` fonction avec votre point de terminaison, clé d’abonnement, l’URL de détection d’anomalie de lot et les données de série chronologique.

2. Désérialiser l’objet JSON et l’écrire dans la console.

3. Si la réponse contient `code` champ, d’imprimer le code d’erreur et un message d’erreur. 

4. Sinon, recherchez les positions d’anomalies dans le jeu de données. La réponse `isAnomaly` champ contient un tableau de valeurs booléennes, chacun d’eux indique si un point de données est une anomalie. Convertir ceci en un tableau de chaînes avec l’objet de réponse `ToObject<bool[]>()` (fonction). Parcourir le tableau et imprime l’index de n’importe quel `true` valeurs. Ces valeurs correspondent à l’index de points de données anormales, si un a été trouvé.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter les anomalies le dernier point de données

1. Créer une nouvelle fonction appelée `detectAnomaliesLatest()`. Construire la demande et l’envoyer en appelant le `Request()` fonction avec votre point de terminaison, clé d’abonnement, l’URL de détection des anomalies du dernier point et les données de série chronologique.

2. Désérialiser l’objet JSON et l’écrire dans la console.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la demande

1. Dans la méthode principale de votre application, chargez vos données chronologiques JSON avec `File.ReadAllText()`. 

2. Appelez les fonctions de détection d’anomalie créées ci-dessus. Utilisez `System.Console.ReadKey()` pour maintenir la fenêtre de console ouverte après l’exécution de l’application.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de réponse détection point plus récente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
