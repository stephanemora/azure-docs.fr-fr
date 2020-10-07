---
title: 'Démarrage rapide : Entraîner un modèle et extraire des données de formulaire à l’aide d’une API REST avec Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous utilisez l’API REST Form Recognizer avec Python pour entraîner un modèle et extraire des données à partir de formulaires.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: fa7a954c9855f12e09f95bbf53730d5e5a789c71
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756788"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec Python

Dans ce guide de démarrage rapide, vous utilisez l’API REST Azure Form Recognizer avec Python pour entraîner et scorer des formulaires afin d’extraire des paires clé-valeur et des tables.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :
- [Python](https://www.python.org/downloads/) doit être installé (si vous souhaitez exécuter l’exemple en local).
- Au minimum un ensemble de cinq formulaires du même type. Vous allez utiliser ces données pour entraîner le modèle. Vos formulaires peuvent être de différents types de fichiers, mais ils doivent être du même type de document. Vous pouvez utiliser un [exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) pour ce guide de démarrage rapide. Chargez les fichiers d’apprentissage à la racine d’un conteneur de stockage d’objets blob dans un compte Stockage Azure offrant un niveau de performance standard.

> [!NOTE]
> Ce guide de démarrage rapide utilise des documents distants accessibles par URL. Pour utiliser des fichiers locaux, consultez la [documentation de référence](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync).


## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Entraîner un modèle Form Recognizer

Avant toute chose, vous avez besoin d’un jeu de données d’entraînement dans un conteneur Azure Storage Blob. Vous devez disposer au minimum de cinq formulaires remplis (documents PDF et/ou images) d’un type ou d’une structure identique en tant que données d’entrée principales. Vous pouvez également utiliser un formulaire vide avec deux formulaires remplis. Le nom de fichier du formulaire vide doit inclure le mot « vide ». Consultez [Créer un jeu de données d’apprentissage pour un modèle personnalisé](../build-training-data-set.md) pour obtenir des conseils et des options pour constituer vos données d’apprentissage.

> [!NOTE]
> Vous pouvez utiliser la fonctionnalité de données étiquetées pour étiqueter manuellement une partie ou la totalité de vos données d’entraînement. Il s’agit d’un processus plus complexe, mais le modèle entraîné qui en résulte est de meilleure qualité. Consultez la section [Effectuer un entraînement avec des étiquettes](../overview.md#train-with-labels) de la vue d’ensemble pour en savoir plus.

Pour entraîner un modèle Form Recognizer à l’aide des documents de votre conteneur d’objets blob Azure, appelez l’API **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** (Entraîner un modèle personnalisé) en exécutant le code Python ci-dessous. Avant d’exécuter le code, apportez les modifications suivantes :

1. Remplacez `<SAS URL>` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. Pour récupérer l’URL SAS, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Remplacez `<subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `<endpoint>` par l’URL du point de terminaison pour votre ressource Form Recognizer.
1. Remplacez `<Blob folder name>` par le chemin du dossier dans le stockage d’objets blob où se trouvent vos formulaires. Si vos formulaires sont à la racine de votre conteneur, laissez cette chaîne vide.

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```python
    ########### Python Form Recognizer Labeled Async Train #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
    source = r"<SAS URL>"
    prefix = "<Blob folder name>"
    includeSubFolders = False
    useLabelFile = False
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subsription key>',
    }
    
    body =     {
        "source": source,
        "sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
        },
        "useLabelFile": useLabelFile
    }
    
    try:
        resp = post(url = post_url, json = body, headers = headers)
        if resp.status_code != 201:
            print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
            quit()
        print("POST model succeeded:\n%s" % resp.headers)
        get_url = resp.headers["location"]
    except Exception as e:
        print("POST model failed:\n%s" % str(e))
        quit() 
    ```    
    # <a name="v21-preview"></a>[v2.1 (préversion)](#tab/v2-1)
    ```python
    ########### Python Form Recognizer Labeled Async Train #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    post_url = endpoint + r"/formrecognizer/v2.1-preview.1/custom/models"
    source = r"<SAS URL>"
    prefix = "<Blob folder name>"
    includeSubFolders = False
    useLabelFile = False
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subsription key>',
    }
    
    body =     {
        "source": source,
        "sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
        },
        "useLabelFile": useLabelFile
    }
    
    try:
        resp = post(url = post_url, json = body, headers = headers)
        if resp.status_code != 201:
            print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
            quit()
        print("POST model succeeded:\n%s" % resp.headers)
        get_url = resp.headers["location"]
    except Exception as e:
        print("POST model failed:\n%s" % str(e))
        quit() 
    ```    


    ---


1. Enregistrez le code dans un fichier avec une extension .py. Par exemple, *form-recognizer-train.py*.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognizer-train.py`.

## <a name="get-training-results"></a>Obtenir les résultats de l’entraînement

Une fois que vous avez démarré l’opération d’entraînement, vous utilisez l’ID retourné pour obtenir l’état de l’opération. Ajoutez le code suivant au bas de votre script Python. Ce code utilise la valeur d’ID de l’appel d’entraînement dans un nouvel appel d’API. L’opération d’entraînement étant asynchrone, ce script appelle l’API à intervalles réguliers jusqu’à ce que le processus d’entraînement soit terminé. Nous recommandons un intervalle d’au moins une seconde.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Une fois le processus d’entraînement terminé, vous recevez une réponse `201 (Success)` avec du contenu JSON comme suit :

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

Copiez la valeur `"modelId"` pour l’utiliser dans les étapes suivantes.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Une fois le processus terminé, vous recevez une réponse `200 (Success)` avec du contenu JSON sous la forme suivante. La réponse a été raccourcie à des fins de simplicité. Les tableaux et les associations clé/valeur principales se trouvent dans le nœud `"pageResults"`. Si vous avez également spécifié l’extraction de texte brut par le biais du paramètre d’URL *includeTextDetails*, le nœud `"readResults"` affiche le contenu et les positions de tout le texte dans le document.


Cet exemple de sortie JSON a été raccourci pour simplifier.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v2.1 (préversion)](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 

---


## <a name="improve-results"></a>Améliorer les résultats

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé les API REST Form Recognizer avec Python pour entraîner un modèle et l’exécuter dans un exemple de scénario. Consultez à présent la documentation de référence pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
