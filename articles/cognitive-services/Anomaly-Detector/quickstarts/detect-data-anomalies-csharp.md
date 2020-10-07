---
title: 'Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et C#'
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les deux modes de détection de l’API Détecteur d’anomalies afin de détecter les anomalies dans vos données de séries chronologiques.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: c19f901f4419cca59a3b4e070191c8bb89ce7c34
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319195"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et C#

Utilisez ce guide de démarrage rapide pour commencer à utiliser l’API Détecteur d’anomalies afin de détecter les anomalies dans vos données de série chronologique. Cette application C# envoie des demandes d’API contenant des données de série chronologique au format JSON et reçoit les réponses.

| Requête d’API                                        | Sortie de l’application                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter des anomalies par lot                        | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour chaque point de données dans les données de la série chronologique, et les positions des anomalies détectées. |
| Détecter l’état d’anomalie du dernier point de données | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour le dernier point de données dans les données de série chronologique. |
| Détecter les points de changement qui marquent les nouvelles tendances des données | Réponse JSON contenant les points de changement détectés dans les données de série chronologique. |

Bien que cette application soit écrite en C#, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Vous trouverez le code source de ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
- Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Créer une ressource Détecteur d’anomalies"  target="_blank">créez une ressource Détecteur d’anomalies <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Attendez qu’elle se déploie, puis cliquez sur le bouton **Accéder à la ressource**.
    - Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Détecteur d’anomalies. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
- N’importe quelle édition de [Visual Studio 2017 ou ultérieure](https://visualstudio.microsoft.com/downloads/)
- Le framework [Json.NET](https://www.newtonsoft.com/json), disponible sous forme de package NuGet. Pour installer Newtonsoft.Json en tant que package NuGet dans Visual Studio :

    1. Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions**.
    2. Sélectionnez **Gérer les packages NuGet**.
    3. Recherchez *Newtonsoft.Json* et installez le package.

- Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](https://www.mono-project.com/).

- Fichier JSON contenant des points de données de séries chronologiques. Les exemples de données ce guide de démarrage rapide sont disponibles sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Dans Visual Studio, créez une solution de console et ajoutez les packages suivants.

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Créez des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection d’anomalies. Ceux-ci seront ajoutés ultérieurement à votre point de terminaison de service pour créer les URL de requête de l’API.

    | Méthode de détection                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Détection par lot                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Détection sur le dernier point de données | `/anomalydetector/v1.0/timeseries/last/detect`   |
    | Détection des points de changement | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

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

## <a name="detect-change-points-in-the-data"></a>Détecter les points de changement dans les données

1. Créez une fonction appelée `detectChangePoints()`. Construisez la demande et envoyez-la en appelant la fonction `Request()` avec votre point de terminaison, l’URL de détection d’anomalies par lots, votre clé d’abonnement et les données de série chronologique.

2. Désérialisez l’objet JSON et écrivez-le dans la console.

3. Si la réponse contient un champ `code`, imprimez le code d’erreur et le message d’erreur.

4. Sinon, recherchez les positions des points de changement dans le jeu de données. Le champ `isChangePoint` de la réponse contient un tableau de valeurs booléennes, chacune d’elles indiquant si un point de données a été identifié en tant que point de changement. Convertissez-le en tableau de chaînes avec la fonction `ToObject<bool[]>()` de l’objet de réponse. Itérez au sein du tableau et imprimez l’index des valeurs `true`. Ces valeurs correspondent aux indices des points de changement de tendance, si de tels points sont trouvés.

    [!code-csharp[Detect change points](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectChangePoints)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la requête

1. Dans la méthode Main de votre application, chargez vos données de série chronologique JSON avec `File.ReadAllText()`.

2. Appelez les fonctions de détection d’anomalie créées plus haut. Utilisez `System.Console.ReadKey()` pour maintenir la fenêtre de console ouverte après l’exécution de l’application.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de dernière réponse de détection de points](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Exemple de réponse de détection de points de changement](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
