---
title: 'Démarrage rapide : Détecter des anomalies en tant que lot avec l’API Détecteur d’anomalies et Python'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Détecteur d’anomalies pour détecter les anomalies dans vos séries de données en lot ou en streaming avec ce guide de démarrage rapide.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 38c2b3cdf40f1924a36ffd84d9dc5f9b2f7f319d
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245704"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec l’API Détecteur d’anomalies et Python

Utilisez ce guide de démarrage rapide pour commencer à utiliser les deux modes de détection de l’API Détecteur d’anomalies afin de détecter les anomalies dans vos données de séries chronologiques. Cette application Python envoie deux requêtes d’API contenant des données de séries chronologiques au format JSON, et reçoit les réponses.

| Requête d’API                                        | Sortie de l’application                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter des anomalies par lot                        | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour chaque point de données dans les données de la série chronologique, et les positions des anomalies détectées. |
| Détecter l’état d’anomalie du dernier point de données | La réponse JSON contenant l’état de l’anomalie (et d’autres données) pour le dernier point de données dans les données de série chronologique.                                                                                                                                         |

 Bien que cette application soit écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Vous trouverez le code source de ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
- Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Créer une ressource Détecteur d’anomalies"  target="_blank">créez une ressource Détecteur d’anomalies <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Attendez qu’elle se déploie, puis cliquez sur le bouton **Accéder à la ressource**.
    - Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Détecteur d’anomalies. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
- [Python 2.x ou 3.x](https://www.python.org/downloads/)
- [Bibliothèque de requêtes](https://pypi.org/project/requests/) pour Python

- Fichier JSON contenant des points de données de séries chronologiques. Les exemples de données ce guide de démarrage rapide sont disponibles sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Créez un fichier Python et ajoutez les importations suivantes.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Créez des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection d’anomalies. Ceux-ci seront ajoutés ultérieurement à votre point de terminaison de service pour créer les URL de requête de l’API.

    |Méthode de détection  |URI  |
    |---------|---------|
    |Détection par lot    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Détection sur le dernier point de données     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Lisez le fichier de données JSON en l’ouvrant et en utilisant `json.load()`.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des requêtes

1. Créez une fonction appelée `send_request()` qui sélectionne les variables créées précédemment. Ensuite, effectuez les étapes suivantes.

2. Créez un dictionnaire pour les en-têtes de requête. Définissez `Content-Type` sur `application/json`, et ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

3. Envoyez la requête à l’aide de `requests.post()`. Combinez l’URL de votre point de terminaison et l’URL de détection d’anomalie pour l’URL de requête complète, et incluez vos en-têtes, et les données de requête json. Retournez ensuite la réponse.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Détecter des anomalies par lot

1. Créez une méthode appelée `detect_batch()` pour détecter les anomalies dans le jeu de données sous forme de lot. Appelez la méthode `send_request()` créée ci-dessus avec votre point de terminaison, l’URL, la clé d’abonnement et les données JSON.

2. Appelez `json.dumps()` sur le résultat à mettre en forme, et imprimez-le sur la console.

3. Si la réponse contient un champ `code`, imprimez le code d’erreur et le message d’erreur.

4. Sinon, trouvez les positions des anomalies dans le jeu de données. Le champ `isAnomaly` de la réponse contient une valeur booléenne indiquant si un point de données particulier est une anomalie. Itérez dans la liste et imprimez l’index des valeurs `True`. Ces valeurs correspondent à l’index des points de données anormaux, le cas échéant.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

1. Créez une méthode appelée `detect_latest()` pour déterminer si le dernier point de données de votre série chronologique est une anomalie. Appelez la méthode `send_request()` ci-dessus avec votre point de terminaison, url, clé d’abonnement et données json.

2. Appelez `json.dumps()` sur le résultat à mettre en forme, et imprimez-le sur la console.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Envoyer la demande

Appelez les méthodes de détection d’anomalies créées ci-dessus.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de dernière réponse de détection de points](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
