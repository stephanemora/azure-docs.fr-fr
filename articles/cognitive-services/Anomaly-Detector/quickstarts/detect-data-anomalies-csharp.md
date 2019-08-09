---
title: 'Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et C#'
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
ms.openlocfilehash: 97efa5cd91646809178d685ca51e29ef2fda7c0d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564739"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et C# 

Utilisez ce guide de démarrage rapide pour commencer à utiliser les deux modes de détection de l’API Détecteur d’anomalies afin de détecter les anomalies dans vos données de séries chronologiques. Cette application C# envoie deux requêtes d’API contenant des données de séries chronologiques au format JSON, et reçoit les réponses.

| Requête d’API                                        | Sortie de l’application                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter des anomalies en tant que lot                        | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour chaque point de données dans les données de la série chronologique, et les positions des anomalies détectées. |
| Détecter l’état d’anomalie du dernier point de données | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour le dernier point de données dans les données de série chronologique.                                                                                                                                         |

 Bien que cette application soit écrite en C#, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Prérequis

- N’importe quelle édition de [Visual Studio 2017 ou version ultérieure](https://visualstudio.microsoft.com/downloads/)

- Le framework [Json.NET](https://www.newtonsoft.com/json), disponible sous forme de package NuGet. Pour installer Newtonsoft.Json en tant que package NuGet dans Visual Studio :
    
    1. Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions**.
    2. Sélectionnez **Gérer les packages NuGet**.
    3. Recherchez *Newtonsoft.Json* et installez le package.

- Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](https://www.mono-project.com/).

- Fichier JSON contenant des points de données de séries chronologiques. Les exemples de données ce guide de démarrage rapide sont disponibles sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Dans Visual Studio, créez une solution de console et ajoutez les packages suivants. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Créez des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection d’anomalies. Ceux-ci seront ajoutés ultérieurement à votre point de terminaison de service pour créer les URL de requête de l’API.

    |Méthode de détection  |URI  |
    |---------|---------|
    |Détection par lot    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
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

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des requêtes

1. Créez une fonction asynchrone appelée `Request` qui prend les variables créées plus haut.

2. Définissez le protocole de sécurité et les informations d’en-tête du client à l’aide d’un objet `HttpClient`. Veillez à ajouter votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`. Créez ensuite un objet `StringContent` pour la requête.

3. Envoyez la requête avec `PostAsync()`, puis retournez la réponse.

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

## <a name="detect-anomalies-as-a-batch"></a>Détecter des anomalies par lot

1. Créez une fonction appelée `detectAnomaliesBatch()`. Construisez la requête et envoyez-la en appelant la fonction `Request()` avec votre point de terminaison, la clé d’abonnement, l’URL de détection d’anomalie par lot et les données de série chronologique.

2. Désérialisez l’objet JSON et écrivez-le dans la console.

3. Si la réponse contient un champ `code`, imprimez le code d’erreur et le message d’erreur. 

4. Sinon, trouvez les positions des anomalies dans le jeu de données. Le champ `isAnomaly` de la réponse contient un tableau de valeurs booléennes, chacune indiquant si un point de données est une anomalie. Convertissez-le en tableau de chaînes avec la fonction `ToObject<bool[]>()` de l’objet de réponse. Itérez au sein du tableau et imprimez l’index des valeurs `true`. Ces valeurs correspondent à l’index des points de données anormaux, le cas échéant.

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

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

1. Créez une fonction appelée `detectAnomaliesLatest()`. Construisez la requête et envoyez-la en appelant la fonction `Request()` avec votre point de terminaison, la clé d’abonnement, l’URL de détection d’anomalie du dernier point et les données de série chronologique.

2. Désérialisez l’objet JSON et écrivez-le dans la console.

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

## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la requête

1. Dans la méthode Main de votre application, chargez vos données de série chronologique JSON avec `File.ReadAllText()`. 

2. Appelez les fonctions de détection d’anomalie créées plus haut. Utilisez `System.Console.ReadKey()` pour maintenir la fenêtre de console ouverte après l’exécution de l’application.

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
* [Exemple de dernière réponse de détection de points](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
