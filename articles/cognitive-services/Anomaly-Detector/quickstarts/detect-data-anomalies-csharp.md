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
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 76308e2167cbedae9572f1fb5037dfb394ce4b17
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483422"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et C# 

Utilisez ce guide de démarrage rapide pour commencer à utiliser les deux modes de détection de l’API Détecteur d’anomalies afin de détecter les anomalies dans vos données de séries chronologiques. Cette application C# envoie deux requêtes d’API contenant des données de séries chronologiques au format JSON, et reçoit les réponses.

| Requête d’API                                        | Sortie de l’application                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter des anomalies en tant que lot                        | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour chaque point de données dans les données de la série chronologique, et les positions des anomalies détectées. |
| Détecter l’état d’anomalie du dernier point de données | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour le dernier point de données dans les données de série chronologique.                                        |

 Bien que cette application soit écrite en C#, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Vous trouverez le code source de ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Prérequis

- N’importe quelle édition de [Visual Studio 2017 ou version ultérieure](https://visualstudio.microsoft.com/downloads/)
- Une clé et un point de terminaison de détecteur d’anomalie
- Le framework [Json.NET](https://www.newtonsoft.com/json), disponible sous forme de package NuGet. Pour installer Newtonsoft.Json en tant que package NuGet dans Visual Studio :
    
    1. Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions**.
    2. Sélectionnez **Gérer les packages NuGet**.
    3. Recherchez *Newtonsoft.Json* et installez le package.

- Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](https://www.mono-project.com/).

- Fichier JSON contenant des points de données de séries chronologiques. Les exemples de données ce guide de démarrage rapide sont disponibles sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Créer une ressource Détecteur d’anomalies

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Dans Visual Studio, créez une solution de console et ajoutez les packages suivants. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Créez des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection d’anomalies. Ceux-ci seront ajoutés ultérieurement à votre point de terminaison de service pour créer les URL de requête de l’API.

    | Méthode de détection                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Détection par lot                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Détection sur le dernier point de données | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des requêtes

1. Créez une fonction asynchrone appelée `Request` qui prend les variables créées plus haut.

2. Définissez le protocole de sécurité et les informations d’en-tête du client à l’aide d’un objet `HttpClient`. Veillez à ajouter votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`. Créez ensuite un objet `StringContent` pour la requête.

3. Envoyez la requête avec `PostAsync()`, puis retournez la réponse.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Détecter des anomalies par lot

1. Créez une fonction appelée `detectAnomaliesBatch()`. Construisez la requête et envoyez-la en appelant la fonction `Request()` avec votre point de terminaison, la clé d’abonnement, l’URL de détection d’anomalie par lot et les données de série chronologique.

2. Désérialisez l’objet JSON et écrivez-le dans la console.

3. Si la réponse contient un champ `code`, imprimez le code d’erreur et le message d’erreur. 

4. Sinon, trouvez les positions des anomalies dans le jeu de données. Le champ `isAnomaly` de la réponse contient un tableau de valeurs booléennes, chacune indiquant si un point de données est une anomalie. Convertissez-le en tableau de chaînes avec la fonction `ToObject<bool[]>()` de l’objet de réponse. Itérez au sein du tableau et imprimez l’index des valeurs `true`. Ces valeurs correspondent à l’index des points de données anormaux, le cas échéant.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

1. Créez une fonction appelée `detectAnomaliesLatest()`. Construisez la requête et envoyez-la en appelant la fonction `Request()` avec votre point de terminaison, la clé d’abonnement, l’URL de détection d’anomalie du dernier point et les données de série chronologique.

2. Désérialisez l’objet JSON et écrivez-le dans la console.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la requête

1. Dans la méthode Main de votre application, chargez vos données de série chronologique JSON avec `File.ReadAllText()`. 

2. Appelez les fonctions de détection d’anomalie créées plus haut. Utilisez `System.Console.ReadKey()` pour maintenir la fenêtre de console ouverte après l’exécution de l’application.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de dernière réponse de détection de points](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
