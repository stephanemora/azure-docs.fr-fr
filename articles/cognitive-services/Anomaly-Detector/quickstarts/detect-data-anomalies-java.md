---
title: 'Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et Java'
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Détecteur d’anomalies pour détecter les anomalies dans vos séries de données, soit par lot, soit sur des données de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239049"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et Java

Utilisez ce guide de démarrage rapide pour commencer à utiliser les deux modes de détection de l’API Détecteur d’anomalies afin de détecter les anomalies dans vos données de séries chronologiques. Cette application Java envoie deux requêtes d’API contenant des données de séries chronologiques au format JSON, et obtient les réponses.

| Requête d’API                                        | Sortie de l’application                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter des anomalies par lot                        | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour chaque point de données dans les données de la série chronologique, et les positions des anomalies détectées. |
| Détecter l’état d’anomalie du dernier point de données | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour le dernier point de données dans les données de série chronologique.                                                                                                                                         |

 Bien que cette application soit écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Vous trouverez le code source de ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Prérequis

- [Kit de développement Java&trade; (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure
- Une clé et un point de terminaison de détecteur d’anomalie
- Importez ces bibliothèques à partir du référentiel Maven :
    - Package [JSON en Java](https://mvnrepository.com/artifact/org.json/json)
    - Package [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Fichier JSON contenant des points de données de séries chronologiques. Les exemples de données ce guide de démarrage rapide sont disponibles sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Créer une ressource Détecteur d’anomalies

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Créez un projet Java et importez les bibliothèques suivantes.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Créez des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection d’anomalies. Ceux-ci seront ajoutés ultérieurement à votre point de terminaison de service pour créer les URL de requête de l’API.

    |Méthode de détection  |URI  |
    |---------|---------|
    |Détection par lot    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Détection sur le dernier point de données     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des requêtes

1. Créez une fonction appelée `sendRequest()` qui sélectionne les variables créées précédemment. Ensuite, effectuez les étapes suivantes.

2. Créez un objet `CloseableHttpClient` qui peut envoyer des requêtes à l’API. Envoyez la requête à un objet de requête `HttpPost` en combinant votre point de terminaison et une URL Détecteur d’anomalies.

3. Utilisez la fonction `setHeader()` de la requête pour affecter la valeur `application/json` à l’en-tête `Content-Type`, et ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

4. Utilisez la fonction `setEntity()` de la requête sur les données à envoyer.

5. Utilisez la fonction `execute()` du client pour envoyer la requête, et enregistrez-la dans un objet `CloseableHttpResponse`.

6. Créez un objet `HttpEntity` pour stocker le contenu de la réponse. Obtenez le contenu avec `getEntity()`. Si la réponse n’est pas vide, retournez-la.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Détecter des anomalies par lot

1. Créez une méthode appelée `detectAnomaliesBatch()` pour détecter les anomalies dans le jeu de données sous forme de lot. Appelez la méthode `sendRequest()` créée ci-dessus avec votre point de terminaison, l’URL, la clé d’abonnement et les données JSON. Obtenez le résultat et imprimez-le dans la console.

2. Si la réponse contient un champ `code`, imprimez le code d’erreur et le message d’erreur.

3. Sinon, trouvez les positions des anomalies dans le jeu de données. Le champ `isAnomaly` de la réponse contient une valeur booléenne indiquant si un point de données particulier est une anomalie. Obtenez et effectuez une itération au sein du tableau JSON, en imprimant l’index des valeurs `true`. Ces valeurs correspondent à l’index des points de données anormaux, le cas échéant.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

Créez une méthode appelée `detectAnomaliesLatest()` pour détecter l’état d’anomalie du dernier point de données dans le jeu de données. Appelez la méthode `sendRequest()` créée ci-dessus avec votre point de terminaison, l’URL, la clé d’abonnement et les données JSON. Obtenez le résultat et imprimez-le dans la console.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la requête

1. Dans la méthode Main de votre application, lisez le fichier JSON contenant les données qui seront ajoutées aux requêtes.

2. Appelez les deux fonctions de détection d’anomalie créées plus haut.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de dernière réponse de détection de points](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
