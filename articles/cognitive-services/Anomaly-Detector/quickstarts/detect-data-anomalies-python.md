---
title: 'Démarrage rapide : Détecter les anomalies en tant que lot à l’aide de l’API REST de détecteur d’anomalies et Python | Microsoft Docs'
description: Utiliser l’API de détecteur d’anomalie pour détecter les anomalies dans votre série de données en tant que lot ou diffusion en continu de données.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 6b4ddcadfe63f74d115c155354a276e45c6b53f9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544498"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalies et Python

Utilisez ce guide de démarrage rapide pour commencer à utiliser deux modes de détection de l’API de détecteur d’anomalie pour détecter les anomalies dans vos données de série chronologique. Cette application Python envoie deux demandes API contenant des données de série chronologique au format JSON et obtient les réponses.

| Demande d’API                                        | Sortie de l’application                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Détecter les anomalies en tant que lot                        | La réponse JSON contenant l’état d’anomalie (et autres données) pour chaque point de données dans les données de série chronologique et les positions de toutes les anomalies détectées. |
| Détecter les anomalies le dernier point de données | La réponse JSON contenant l’état d’anomalie (et autres données) pour le dernier point de données dans les données de série chronologique.                                                                                                                                         |

 Bien que cette application soit écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Conditions préalables

- [Python 2.x ou 3.x](https://www.python.org/downloads/)

- Le [bibliothèque de requêtes](http://docs.python-requests.org) pour python

- Une JSON fichier conteneurs données chronologiques pointant. Vous trouverez l’exemple de données pour ce démarrage rapide sur [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Créer une application

1. Dans votre éditeur de texte ou un IDE, créez un nouveau fichier python. Ajoutez les importations ci-après.

    ```python
    import requests
    import json
    ```

2. Créer des variables pour votre clé d’abonnement et votre point de terminaison. Voici les URI que vous pouvez utiliser pour la détection des anomalies. Ils seront ajoutés à votre point de terminaison de service ultérieurement pour créer l’API URL de demande.

    |Méthode de détection  |URI  |
    |---------|---------|
    |Détection de lot    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Détection sur le dernier point de données     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Lire le fichier de données JSON en ouvrant et en utilisant `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des demandes

1. Créer une nouvelle fonction appelée `send_request()` qui accepte les variables créées ci-dessus. Ensuite, effectuez les étapes suivantes.

2. Créer un dictionnaire pour les en-têtes de demande. Définir le `Content-Type` à `application/json`et ajoutez votre clé d’abonnement pour le `Ocp-Apim-Subscription-Key` en-tête.

3. Envoyer la demande à l’aide `requests.post()`. Combinez votre URL de détection des anomalies et de point de terminaison pour l’URL de demande complète et inclure vos en-têtes et les données de demande json. Puis retourner la réponse.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Détecter les anomalies en tant que lot

1. Créer une méthode appelée `detect_batch()` pour détecter les anomalies dans les données en tant que lot. Appelez le `send_request()` méthode créé ci-dessus avec votre point de terminaison, url, clé d’abonnement et les données json.

2. Appeler `json.dumps()` sur le résultat à mettre en forme et l’imprimer à la console.

3. Si la réponse contient `code` champ, d’imprimer le code d’erreur et un message d’erreur.

4. Sinon, recherchez les positions d’anomalies dans le jeu de données. La réponse `isAnomaly` champ contient une valeur booléenne relatives aux si un point de données donné est une anomalie. Effectuer une itération dans la liste et imprimer l’index de n’importe quel `True` valeurs. Ces valeurs correspondent à l’index de points de données anormales, si un a été trouvé.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter les anomalies le dernier point de données

1. Créer une méthode appelée `detect_latest()` pour déterminer si le dernier point de données dans votre série chronologique est une anomalie. Appelez le `send_request()` méthode mentionnée ci-dessus avec votre point de terminaison, url, clé d’abonnement et les données json. 

2. Appeler `json.dumps()` sur le résultat à mettre en forme et l’imprimer à la console.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Charger vos données de série chronologique et envoyer la demande

1. Charger vos données de série chronologique JSON ouvrant un gestionnaire de fichiers et en utilisant `json.load()` dessus. Appelez ensuite l’anomalie des méthodes de détection créés ci-dessus.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Exemple de réponse

Une réponse correcte est retournée au format JSON. Cliquez sur les liens ci-dessous pour afficher la réponse JSON sur GitHub :
* [Exemple de réponse de détection par lots](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemple de réponse détection point plus récente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)