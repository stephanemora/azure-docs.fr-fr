---
title: Démarrage rapide avec la bibliothèque de client Python Détecteur d’anomalies (multivarié)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 9b848f6c86f2ff2e95fa5cc191b088b7175f2311
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318752"
---
Démarrez avec la bibliothèque de client Détecteur d’anomalies (multivarié) pour Python. Procédez comme suit pour installer le package de démarrage à l’aide des algorithmes fournis par le service. Les nouvelles API de détection d’anomalie multivariée permettent aux développeurs d’intégrer facilement l’intelligence artificielle avancée pour détecter les anomalies à partir de groupes de métriques, sans avoir besoin d’une connaissance du machine learning ni de données étiquetées. Les dépendances et inter-corrélations entre différents signes sont automatiquement comptabilisées comme des facteurs clés. Cela vous permet de protéger de manière proactive vos systèmes complexes contre les défaillances.

Utilisez la bibliothèque de client Détecteur d’anomalies (multivarié) pour Python pour :

* Détecter les anomalies au niveau du système à partir d’un groupe de séries chronologiques.
* Quand des séries chronologiques individuelles ne contiennent pas beaucoup d’informations et que vous devez examiner tous les signes pour détecter un problème.
* La maintenance prédictive des ressources physiques coûteuses avec des dizaines ou des centaines de types de capteurs différents mesurant divers aspects de l’intégrité du système.

## <a name="prerequisites"></a>Prérequis

* [Python 3.x](https://www.python.org/)
* [Bibliothèque d’analyse de données Pandas](https://pandas.pydata.org/)
* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Créer une ressource Détecteur d’anomalies"  target="_blank">créez une ressource Détecteur d’anomalies </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Attendez qu’elle se déploie, puis cliquez sur le bouton **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Détecteur d’anomalies. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.


## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

 Créez un fichier Python et importez les bibliothèques suivantes.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Créez des variables pour votre clé : une variable d’environnement, le chemin d’un fichier de données de série chronologique et l’emplacement Azure de votre abonnement. Par exemple : `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les opérations suivantes avec la bibliothèque de client Détecteur d’anomalies pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Formation du modèle](#train-the-model)
* [Détecter les anomalies](#detect-anomalies)
* [Exporter le modèle](#export-model)
* [Supprimer le modèle](#delete-model)

## <a name="authenticate-the-client"></a>Authentifier le client

Pour instancier un nouveau client Détecteur d’anomalies, vous devez passer la clé d’abonnement du Détecteur d’anomalies et le point de terminaison associé. Nous allons également établir une source de données.  

Pour utiliser les API Détecteur d’anomalies (multivarié), nous devons entraîner notre propre modèle avant d’utiliser la détection. Les données utilisées pour l’entraînement constituent un lot de séries chronologiques. Chaque série chronologique doit être au format CSV avec deux colonnes : horodatage et valeur. Toutes les séries chronologiques doivent être compressées dans un fichier zip et être chargées dans le [Stockage Blob Azure](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Par défaut, le nom de fichier est utilisé pour représenter la variable pour la série chronologique. En guise d’alternative, un fichier meta.json supplémentaire peut être inclus dans le fichier zip si vous souhaitez que le nom de la variable soit différent du nom du fichier .zip. Une fois que nous avons généré une [URL SAS (signatures d’accès partagé) de blob](../../../../storage/common/storage-sas-overview.md), nous pouvons l’utiliser dans le fichier zip pour l’entraînement.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Effectuer l’apprentissage du modèle

Nous allons tout d’abord entraîner le modèle, vérifier son état pendant l’entraînement pour déterminer à quel moment ce dernier est terminé, puis récupérer l’ID de modèle le plus récent dont nous aurons besoin quand nous passerons à la phase de détection.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Détecter les anomalies

Utilisez `detect_anomaly` et `get_dectection_result` pour déterminer s’il existe des anomalies dans votre source de données. Vous devez passer l’ID du modèle que vous venez d’entraîner.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Exporter le modèle

Si vous voulez exporter un modèle, utilisez `export_model` et passez l’ID du modèle à exporter :

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Supprimer le modèle

Pour supprimer un modèle, utilisez `delete_multivariate_model` et passez l’ID du modèle à supprimer :

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Exécution de l'application

Avant d’exécuter l’application, nous devons ajouter du code pour appeler nos fonctions nouvellement créées.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Exécutez l’application avec la commande `python` et le nom de votre fichier.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
