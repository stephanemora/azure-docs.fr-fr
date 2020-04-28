---
title: Guide de démarrage rapide de la bibliothèque de client Python du Détecteur d’anomalies
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759818"
---
Commencez à utiliser la bibliothèque de client Détecteur d’anomalies pour Python. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Le service Détecteur d’anomalies vous permet de rechercher des anomalies dans vos données de séries chronologiques en utilisant automatiquement les modèles les mieux adaptés sur celles-ci, quel que soit le secteur d’activité, le scénario ou le volume de données.

Utilisez la bibliothèque de client Détecteur d’anomalies pour Python pour :

* Détecter les anomalies dans votre jeu de données de série chronologique, comme une requête de lots
* Détecter l’état d’anomalie du dernier point de données dans vos séries chronologiques

[Documentation de référence de la bibliothèque](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [Trouver l’exemple de code sur GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Une clé et un point de terminaison de détecteur d’anomalie
* [Python 3.x](https://www.python.org/)
* [Bibliothèque d’analyse de données Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Configuration

### <a name="create-an-anomaly-detector-resource"></a>Créer une ressource Détecteur d’anomalies

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Créer une application Python

 Créez un fichier Python et importez les bibliothèques suivantes.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Créez des variables pour votre clé en tant que variable d’environnement, le chemin d’accès à un fichier de données de série chronologique et l’emplacement Azure de votre abonnement. Par exemple : `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Modèle objet

Le client Détecteur d’anomalies est un objet [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client propose deux méthodes de détection des anomalies : Sur un jeu de données entier à l’aide de [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) et sur les derniers points de données à l’aide de [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Les données de séries chronologiques sont envoyées en tant que série de [points](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) dans un objet [Requête](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python). L’objet `Request` contient des propriétés pour décrire les données ([Granularité](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) par exemple) et des paramètres pour la détection d’anomalies. 

La réponse de Détecteur d’anomalies est un objet [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) ou [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) selon la méthode utilisée. 

## <a name="code-examples"></a>Exemples de code 

Ces extraits de code vous montrent comment effectuer les opérations suivantes avec la bibliothèque de client Détecteur d’anomalies pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Charger un jeu de données de séries chronologiques à partir d’un fichier](#load-time-series-data-from-a-file)
* [Détecter des anomalies dans un jeu de données entier](#detect-anomalies-in-the-entire-data-set) 
* [Détecter l’état d’anomalie du dernier point de données](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Authentifier le client

Ajoutez votre variable d’emplacement Azure au point de terminaison et authentifiez le client avec votre clé.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Charger des données de séries chronologiques à partir d’un fichier

Téléchargez l’exemple de données pour ce démarrage rapide à partir de [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) :
1. Dans votre navigateur, cliquez avec le bouton droit sur **Brutes**.
2. Cliquez sur **Enregistrer le lien sous**.
3. Enregistrez le fichier dans le répertoire de votre application en tant que fichier .csv.

Ces données de séries chronologiques sont mises en forme dans un fichier .csv et envoyées à l’API Détecteur d’anomalies.

Chargez votre fichier de données avec la méthode `read_csv()` de la bibliothèque Pandas et créez une variable de liste vide pour stocker vos séries de données. Effectuez une itération dans le fichier et ajoutez les données en tant qu'objet [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python). Cet objet contient le timestamp et une valeur numérique issus des lignes de votre fichier de données .csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Créez un objet [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) avec vos séries chronologiques, et la [granularité](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (ou périodicité) de ses points de données. Par exemple : `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Détecter des anomalies dans un jeu de données entier 

Appelez l’API pour détecter les anomalies dans l'ensemble des données de séries chronologiques à l'aide de la méthode [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) du client. Stockez l'objet [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) renvoyé. Itérez dans la liste `is_anomaly` de la réponse et imprimez l'index des valeurs `true`. Ces valeurs correspondent à l’index des points de données anormaux, le cas échéant.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

Appelez l’API Détecteur d’anomalies pour déterminer si votre dernier point de données relève d'une anomalie à l’aide de la méthode [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) du client, et stockez l'objet [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) renvoyé. La valeur `is_anomaly` de la réponse correspond à une valeur booléenne qui spécifie l’état d'anomalie de ce point.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python` et le nom de votre fichier.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
