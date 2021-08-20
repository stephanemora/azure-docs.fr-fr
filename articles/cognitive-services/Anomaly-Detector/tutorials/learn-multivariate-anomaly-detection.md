---
title: 'Tutoriel : Découvrir la détection d’anomalie multivariée en une heure'
titleSuffix: Azure Cognitive Services
description: Tutoriel de bout en bout sur la détection d’anomalie multivariée.
services: cognitive-services
author: juaduan
manager: conhua
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 06/27/2021
ms.author: juaduan
ms.openlocfilehash: bdebf242dcea8e7640d68afcb21d7ba7e2e49576
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292638"
---
# <a name="tutorial-learn-multivariate-anomaly-detection-in-one-hour"></a>Tutoriel : Découvrir la détection d’anomalie multivariée en une heure

Le Détecteur d’anomalies avec détection d’anomalie multivariée (MVAD) est un outil d’intelligence artificielle (IA) pour la détection des anomalies à partir d’un groupe de métriques de manière **non supervisée**.

En général, pour utiliser la détection MVAD, vous pouvez procéder comme suit :

  1. Créez une ressource du Détecteur d’anomalies qui prend en charge MVAD sur Azure.
  1. Préparez vos données.
  1. Effectuez l’apprentissage d’un modèle MVAD.
  1. Interrogez l’état de votre modèle.
  1. Détectez les anomalies avec le modèle MVAD entraîné.
  1. Récupérez et interprétez les résultats de l’inférence.

Dans ce didacticiel, vous allez :

> [!div class="checklist"]
> * Comprendre comment préparer vos données dans un format correct.
> * Comprendre comment effectuer l’apprentissage et l’inférence avec MVAD.
> * Comprendre les paramètres d’entrée et comment interpréter la sortie dans les résultats d’inférence.

## <a name="1-create-an-anomaly-detector-resource-that-supports-mvad"></a>1. Créer une ressource du Détecteur d’anomalies qui prend en charge MVAD

* Créer un abonnement Azure si vous n’en avez pas – [Créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Une fois que vous avez votre abonnement Azure, [créez une ressource Détecteur d’anomalies](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) dans le portail Azure pour obtenir votre clé API et votre point de terminaison d’API.

> [!NOTE]
> Pendant la phase de préversion, la détection MVAD est disponible uniquement dans certaines régions. Veuillez créer un signet [Nouveautés du Détecteur d’anomalies](../whats-new.md) pour rester informé des déploiements de régions MVAD. Vous pouvez également signaler un problème GitHub ou nous contacter à l’adresse [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com) pour demander l’ajout de régions spécifiques.

## <a name="2-data-preparation"></a>2. Préparation des données

Vous devez ensuite préparer vos données d’apprentissage (et les données d’inférence).

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]

### <a name="tools-for-zipping-and-uploading-data"></a>Outils pour compresser et charger des données

Dans cette section, nous présentons des exemples de code et des outils que vous pouvez copier et modifier pour les ajouter dans votre propre logique d’application qui traite les données d’entrée MVAD.

#### <a name="compressing-csv-files-in-nix"></a>Compression de fichiers CSV dans \*nix

```bash
zip -j series.zip series/*.csv
```

#### <a name="compressing-csv-files-in-windows"></a>Compression de fichiers CSV dans Windows

* Accédez *au* dossier contenant tous les fichiers CSV.
* Sélectionnez tous les fichiers CSV dont vous avez besoin.
* Cliquez avec le bouton droit sur l’un des fichiers CSV et sélectionnez `Send to`.
* Sélectionnez `Compressed (zipped) folder` dans la liste déroulante.
* Renommez le fichier zip si nécessaire.

#### <a name="python-code-zipping--uploading-data-to-azure-blob-storage"></a>Compression du code Python et chargement des données vers Stockage Blob Azure

Vous pouvez vous référer à [ce document](../../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) pour découvrir comment charger un fichier sur un objet blob Azure.

Vous pouvez également vous référer à l’exemple de code ci-dessous, qui peut effectuer la compression et le chargement pour vous. Vous pouvez copier et enregistrer le code Python dans cette section sous la forme d’un fichier .py (par exemple `zipAndUpload.py`) et l’exécuter à l’aide de lignes de commande comme celles-ci :

* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx`

    Cette commande permet de compresser tous les fichiers CSV de `foo\bar` dans un fichier zip unique nommé `test123.zip`. Cette opération charge `test123.zip` dans le conteneur `container_xxx` dans votre objet blob.
* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx -r` 

    Cette commande est similaire à l’opération précédente, mais elle supprime le fichier zip `test123.zip` une fois le chargement réussi. 

Arguments :

* `--source-folder`, `-s`, chemin d’accès au dossier source contenant les fichiers CSV
* `--zipfile-name`, `-z`, nom du fichier zip
* `--connection-string`, `-c`, chaîne de connexion à votre objet blob
* `--container-name`, `-n`, nom du conteneur
* `--remove-zipfile`, `-r`, s’il est activé, supprimer le fichier zip

```python
import os
import argparse
import shutil
import sys

from azure.storage.blob import BlobClient
import zipfile


class ZipError(Exception):
    pass


class UploadError(Exception):
    pass


def zip_file(root, name):
    try:
        z = zipfile.ZipFile(name, "w", zipfile.ZIP_DEFLATED)
        for f in os.listdir(root):
            if f.endswith("csv"):
                z.write(os.path.join(root, f), f)
        z.close()
        print("Compress files success!")
    except Exception as ex:
        raise ZipError(repr(ex))


def upload_to_blob(file, conn_str, cont_name, blob_name):
    try:
        blob_client = BlobClient.from_connection_string(conn_str, container_name=cont_name, blob_name=blob_name)
        with open(file, "rb") as f:
            blob_client.upload_blob(f, overwrite=True)
        print("Upload Success!")
    except Exception as ex:
        raise UploadError(repr(ex))


if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--source-folder", "-s", type=str, required=True, help="path to source folder")
    parser.add_argument("--zipfile-name", "-z", type=str, required=True, help="name of the zip file")
    parser.add_argument("--connection-string", "-c", type=str, help="connection string")
    parser.add_argument("--container-name", "-n", type=str, help="container name")
    parser.add_argument("--remove-zipfile", "-r", action="store_true", help="whether delete the zip file after uploading")
    args = parser.parse_args()

    try:
        zip_file(args.source_folder, args.zipfile_name)
        upload_to_blob(args.zipfile_name, args.connection_string, args.container_name, args.zipfile_name)
    except ZipError as ex:
        print(f"Failed to compress files. {repr(ex)}")
        sys.exit(-1)
    except UploadError as ex:
        print(f"Failed to upload files. {repr(ex)}")
        sys.exit(-1)
    except Exception as ex:
        print(f"Exception encountered. {repr(ex)}")

    try:
        if args.remove_zipfile:
            os.remove(args.zipfile_name)
    except Exception as ex:
        print(f"Failed to delete the zip file. {repr(ex)}")
```

## <a name="3-train-an-mvad-model"></a>3. Effectuer l’apprentissage d’un modèle MVAD

Voici un exemple de corps de la demande et l’exemple de code dans Python pour l’apprentissage d’un modèle MVAD.

```json
// Sample Request Body
{
    "slidingWindow": 200,
    "alignPolicy": {
        "alignMode": "Outer",
        "fillNAMethod": "Linear", 
        "paddingValue": 0
    },
    // This could be your own ZIP file of training data stored on Azure Blob and a SAS url could be used here
    "source": "https://aka.ms/AnomalyDetector/MVADSampleData", 
    "startTime": "2021-01-01T00:00:00Z", 
    "endTime": "2021-01-02T12:00:00Z", 
    "displayName": "Contoso model"
}
```

```python
# Sample Code in Python
########### Python 3.x #############
import http.client, urllib.request, urllib.parse, urllib.error, base64

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '{API key}',
}

params = urllib.parse.urlencode({})

try:
    conn = http.client.HTTPSConnection('{endpoint}')
    conn.request("POST", "/anomalydetector/v1.1-preview/multivariate/models?%s" % params, "{request body}", headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))

####################################
```

Le code de réponse `201` indique une demande réussie.

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

## <a name="4-get-model-status"></a>4. Obtenir l’état du modèle

Étant donné que l’API d’apprentissage est asynchrone, vous n’obtenez pas le modèle immédiatement après l’appel de l’API d’apprentissage. Toutefois, vous pouvez interroger l’état des modèles par clé API, ce qui permet de répertorier tous les modèles, ou par ID de modèle, ce qui permet de répertorier les informations sur le modèle spécifique.

### <a name="list-all-the-models"></a>Répertorier tous les modèles

Vous pouvez vous reporter à [cette page](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/ListMultivariateModel) pour obtenir des informations sur l’URL de la demande et les en-têtes de demande. Notez que nous retournons uniquement 10 modèles classés par heure de mise à jour, mais vous pouvez visiter d’autres modèles en définissant les paramètres `$skip` et `$top` dans l’URL de la demande. Par exemple, si votre URL de demande est `https://{endpoint}/anomalydetector/v1.1-preview/multivariate/models?$skip=10&$top=20`, les 10 derniers modèles sont ignorés et les 20 modèles suivants sont renvoyés.

Voici un exemple de réponse 

```json
{
    "models": [
         {
             "createdTime":"2020-12-01T09:43:45Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:46:13Z",
             "modelId":"b4c1616c-33b9-11eb-824e-0242ac110002",
             "status":"READY",
             "variablesCount":18
         },
         {
             "createdTime":"2020-12-01T09:43:30Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:45:10Z",
             "modelId":"ab9d3e30-33b9-11eb-a3f4-0242ac110002",
             "status":"READY",
             "variablesCount":18
         }
    ],
    "currentCount": 1,
    "maxCount": 50, 
    "nextLink": "<link to more models>"
}
```

La réponse contient 4 champs : `models`, `currentCount`, `maxCount` et `nextLink`.

* `models` contient l’heure de création, l’heure de la dernière mise à jour, l’ID de modèle, le nom d’affichage, le nombre de variables et l’état de chaque modèle.
* `currentCount` contient le nombre de modèles multivariés ayant fait l’objet d’un apprentissage.
* `maxCount` nombre maximal de modèles pris en charge par cette ressource Détecteur d’anomalie.
* `nextLink` peut être utilisé pour récupérer (fetch) d’autres modèles.

### <a name="get-models-by-model-id"></a>Obtenir des modèles par ID de modèle

[Cette page](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetMultivariateModel) décrit l’URL de la requête pour interroger les informations de modèle par ID de modèle. Voici un exemple de réponse

```json
{
        "modelId": "45aad126-aafd-11ea-b8fb-d89ef3400c5f",
        "createdTime": "2020-06-30T00:00:00Z",
        "lastUpdatedTime": "2020-06-30T00:00:00Z",
        "modelInfo": {
          "slidingWindow": 300,
          "alignPolicy": {
            "alignMode": "Outer",
            "fillNAMethod": "Linear",
            "paddingValue": 0
          },
          "source": "<TRAINING_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS>",
          "startTime": "2019-04-01T00:00:00Z",
          "endTime": "2019-04-02T00:00:00Z",
          "displayName": "Devops-MultiAD",
          "status": "READY",
          "errors": [],
          "diagnosticsInfo": {
            "modelState": {
              "epochIds": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100],
              "trainLosses": [0.6291328072547913, 0.1671326905488968, 0.12354248017072678, 0.1025966405868533, 
                              0.0958492755889896, 0.09069952368736267,0.08686016499996185, 0.0860302299260931,
                              0.0828735455870684, 0.08235538005828857],
              "validationLosses": [1.9232804775238037, 1.0645641088485718, 0.6031560301780701, 0.5302737951278687, 
                                   0.4698025286197664, 0.4395163357257843, 0.4182931482799006, 0.4057914316654053, 
                                   0.4056498706340729, 0.3849248886108984],
              "latenciesInSeconds": [0.3398594856262207, 0.3659665584564209, 0.37360644340515137, 
                                     0.3513407707214355, 0.3370304107666056, 0.31876277923583984, 
                                     0.3283309936523475, 0.3503587245941162, 0.30800247192382812,
                                     0.3327946662902832]
            },
            "variableStates": [
              {
                "variable": "ad_input",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              {
                "variable": "ad_ontimer_output",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              // More variables
            ]
          }
        }
      }
```

Vous recevez des informations plus détaillées sur le modèle interrogé. La réponse contient des méta-informations sur le modèle, ses paramètres de formation et des informations de diagnostic. Les informations de diagnostic sont utiles pour le débogage et le suivi de la progression de l’apprentissage.

* `epochIds` indique le nombre d’époques pour lesquelles l’apprentissage du modèle a eu lieu par rapport au nombre total de 100 époques. Par exemple, si le modèle est toujours dans l’état d’apprentissage, la valeur `epochId` peut être `[10, 20, 30, 40, 50]`, ce qui signifie qu’il a effectué l’apprentissage de la 50e époque et qu’il lui reste la moitié à réaliser.
* `trainLosses` et `validationLosses` sont utilisés pour vérifier si la progression de l’optimisation est convergente, auquel cas les deux pertes devraient diminuer progressivement.
* `latenciesInSeconds` contient le coût du temps pour chaque époque et est enregistré toutes les 10 époques. Dans cet exemple, la 10e époque prend environ 0,34 secondes. Cela peut être utile pour estimer l’heure de fin de l’apprentissage.
* `variableStates` résume les informations relatives à chaque variable. Il s’agit d’une liste classée par `filledNARatio` en ordre décroissant. Elle indique le nombre de points de données utilisés pour chaque variable et `filledNARatio` indique le nombre de points manquants. En règle générale, nous devons réduire `filledNARatio` autant que possible.
Un nombre trop important de points de données manquants diminue la précision du modèle.
* Les erreurs lors du traitement des données sont incluses dans le champ `errors`.

## <a name="5-inference-with-mvad"></a>5. Inférence avec MVAD

Pour effectuer l’inférence, il suffit de fournir la source d’objet blob au fichier zip contenant les données d’inférence, l’heure de début et l’heure de fin.

L’inférence est également asynchrone. Les résultats ne sont donc pas retournés immédiatement. Notez que vous devez enregistrer dans une variable le lien des résultats dans **l’en-tête de réponse** qui contient le `resultId`, afin de savoir où trouver les résultats ultérieurement.

Les défaillances sont généralement dues à des problèmes de modèle ou à des problèmes de données. Vous ne pouvez pas effectuer l’inférence si le modèle n’est pas prêt, ni si la liaison de données n’est pas valide. Assurez-vous que les données d’apprentissage et les données d’inférence sont cohérentes, ce qui signifie qu’il doit s’agir **exactement** des mêmes variables, mais avec des horodateurs (timestamps) différents. Si le nombre de variables est inférieur ou supérieur, ou en cas d’inférence avec un ensemble différent de variables, la phase de vérification des données échoue et des erreurs se produisent. La vérification des données est différée, si bien que vous recevez un message d’erreur uniquement quand vous interrogez les résultats.

## <a name="6-get-inference-results"></a>6. Obtenir les résultats de l’inférence

Vous avez besoin de `resultId` pour obtenir les résultats. `resultId` est obtenu à partir de l’en-tête de réponse quand vous envoyez la requête d’inférence. [Cette page](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetDetectionResult) contient des instructions pour interroger les résultats de l’inférence. 

Voici un exemple de réponse

```json
 {
        "resultId": "663884e6-b117-11ea-b3de-0242ac130004",
        "summary": {
          "status": "READY",
          "errors": [],
          "variableStates": [
            {
              "variable": "ad_input",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            {
              "variable": "ad_ontimer_output",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            // more variables
          ],
          "setupInfo": {
            "source": "https://aka.ms/AnomalyDetector/MVADSampleData",
            "startTime": "2019-04-01T00:15:00Z",
            "endTime": "2019-04-01T00:40:00Z"
          }
        },
        "results": [
          {
            "timestamp": "2019-04-01T00:15:00Z",
            "errors": [
              {
                "code": "InsufficientHistoricalData",
                "message": "historical data is not enough."
              }
            ]
          },
          // more results
          {
            "timestamp": "2019-04-01T00:20:00Z",
            "value": {
              "contributors": [],
              "isAnomaly": false,
              "severity": 0,
              "score": 0.17805261260751692
            }
          },
          // more results
          {
            "timestamp": "2019-04-01T00:27:00Z",
            "value": {
              "contributors": [
                {
                  "contributionScore": 0.0007775013367514271,
                  "variable": "ad_ontimer_output"
                },
                {
                  "contributionScore": 0.0007989604079048129,
                  "variable": "ad_series_init"
                },
                {
                  "contributionScore": 0.0008900927229851369,
                  "variable": "ingestion"
                },
                {
                  "contributionScore": 0.008068144477478554,
                  "variable": "cpu"
                },
                {
                  "contributionScore": 0.008222036467507165,
                  "variable": "data_in_speed"
                },
                {
                  "contributionScore": 0.008674941549594993,
                  "variable": "ad_input"
                },
                {
                  "contributionScore": 0.02232242629793674,
                  "variable": "ad_output"
                },
                {
                  "contributionScore": 0.1583773213660846,
                  "variable": "flink_last_ckpt_duration"
                },
                {
                  "contributionScore": 0.9816531517495176,
                  "variable": "data_out_speed"
                }
              ],
              "isAnomaly": true,
              "severity": 0.42135109874230336,
              "score": 1.213510987423033
            }
          },
          // more results
        ]
      }
```

La réponse contient l’état du résultat, les informations sur les variables, les paramètres d’inférence et les résultats de l’inférence.

* `variableStates` répertorie les informations de chaque variable comprise dans la requête d’inférence.
* `setupInfo` correspond au corps de la demande envoyé pour cette inférence.
* `results` contient les résultats de la détection. Il existe trois types de résultats de détection typiques.
    1. Code d’erreur `InsufficientHistoricalData`. Cela se produit généralement uniquement avec les premiers horodateurs, car le modèle réalise l’inférence des données en se basant sur une fenêtre et il a besoin de données historiques pour prendre une décision. Pour les premiers horodateurs, les données historiques sont insuffisantes. L’inférence ne peut donc pas s’effectuer. Dans ce cas, le message d’erreur peut être ignoré.
    1. `"isAnomaly": false` indique que l’horodateur actuel n’est pas une anomalie.
        * `severity ` indique la gravité relative de l’anomalie. Pour les données normales, il s’agit toujours de 0.
        * `score` est la sortie brute du modèle sur laquelle le modèle prend une décision. Elle peut être différente de zéro même pour les points de données normaux.
    1. `"isAnomaly": true` indique une anomalie pour l’horodateur actuel.
        * `severity ` indique la gravité relative de l’anomalie. Pour les données anormales, elle est toujours supérieure à 0.
        * `score` est la sortie brute du modèle sur laquelle le modèle prend une décision. `severity` est une valeur dérivée de `score`. Chaque point de données a un `score`.
        * `contributors` est une liste répertoriant le score de contribution de chaque variable. Les scores de contribution plus élevés indiquent une probabilité plus élevée de la cause racine. Cette liste est souvent utilisée pour interpréter les anomalies, ainsi que pour diagnostiquer les causes racines.

> [!NOTE]
> Une erreur courante consiste à placer tous les points de données avec `isAnomaly`=`true` comme des anomalies. Cela peut entraîner un nombre trop important de faux positifs.
> Vous devez utiliser `isAnomaly` et `severity` (ou `score`) pour déceler les anomalies qui ne sont pas graves et pour utiliser (éventuellement) le regroupement afin de vérifier la durée des anomalies en vue de supprimer le bruit aléatoire. Reportez-vous au [Questions fréquentes (FAQ)](../concepts/best-practices-multivariate.md#faq) dans le document sur les meilleures pratiques pour comprendre la différence entre `severity` et `score`.

## <a name="next-steps"></a>Étapes suivantes

* [Meilleures pratiques : pratiques recommandées lors de l’utilisation des API Détecteur d’anomalies multivarié](../concepts/best-practices-multivariate.md)
* [Démarrage rapide : Utiliser la bibliothèque de client Détecteur d’anomalies multivarié](../quickstarts/client-libraries-multivariate.md)