---
title: 'Démarrage rapide : Effectuer l’entraînement avec des étiquettes en utilisant l’API REST et Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la fonctionnalité de données étiquetées de Form Recognizer avec l’API REST et Python pour entraîner un modèle personnalisé.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: tracking-python
ms.openlocfilehash: 12af4c57fd906d687eedfe7c865d36abaa0da18e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85209145"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Entraîner un modèle Form Recognizer avec des étiquettes à l’aide de l’API REST et de Python

Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Form Recognizer avec Python pour entraîner un modèle personnalisé avec des données étiquetées manuellement. Consultez la section [Effectuer un entraînement avec des étiquettes](../overview.md#train-with-labels) de la vue d’ensemble pour en savoir plus sur cette fonctionnalité.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :
- [Python](https://www.python.org/downloads/) doit être installé (si vous souhaitez exécuter l’exemple en local).
- Au minimum un ensemble de six formulaires du même type. Vous allez utiliser ces données pour entraîner le modèle et tester un formulaire. Vous pouvez utiliser un [exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) pour ce guide de démarrage rapide. Chargez les fichiers d’entraînement à la racine d’un conteneur de stockage d’objets blob dans un compte Stockage Azure.

> [!NOTE]
> Ce guide de démarrage rapide utilise des documents distants accessibles par URL. Pour utiliser des fichiers locaux, consultez la [documentation de référence](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync).

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Configurer les données d’entraînement

Ensuite, vous devez configurer les données d’entrée nécessaires. La fonctionnalité de données étiquetées a des conditions d’entrée spéciales au-delà de celles nécessaires pour entraîner un modèle personnalisé sans étiquettes.

Vérifiez que tous les documents d’entraînement ont le même format. Si vous avez des formulaires dans plusieurs formats, organisez-les en sous-dossiers en fonction du format. Lors de l’entraînement, vous devez diriger l’API vers un sous-dossier.

Pour effectuer l’entraînement d’un modèle à l’aide de données étiquetées, vous avez besoin des fichiers suivants comme entrées dans le sous-dossier. Vous allez apprendre à créer ces fichiers ci-après.

* **Formulaires sources** : formulaires desquels extraire les données. Les types pris en charge sont JPEG, PNG, PDF ou TIFF.
* **Fichiers de disposition OCR** : il s’agit de fichiers JSON qui décrivent la taille et la position de tout texte lisible dans chaque formulaire source. Vous utiliserez l’API de disposition Form Recognizer pour générer ces données. 
* **Fichiers d’étiquettes** : il s’agit de fichiers JSON qui décrivent les étiquettes de données qu’un utilisateur a entrées manuellement.

Tous ces fichiers doivent occuper le même sous-dossier et se présenter sous la forme suivante :

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Quand vous étiquetez des formulaires à l’aide de l’[outil d’étiquetage des exemples](./label-tool.md) Form Recognizer, l’outil crée automatiquement ces fichiers d’étiquette et de disposition OCR.

### <a name="create-the-ocr-output-files"></a>Créer les fichiers de sortie OCR

Les fichiers de résultats OCR permettent au service de prendre en compte les fichiers d’entrée correspondants pour l’entraînement des données étiquetées. Afin d’obtenir les résultats OCR pour un formulaire source donné, effectuez les étapes ci-après :

1. Appelez l’API **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeLayoutAsync)** (Analyser la disposition) sur le conteneur de disposition de lecture avec le fichier d’entrée dans le corps de la requête. Enregistrez l’ID qui se trouve dans l’en-tête **Operation-Location** de la réponse.
1. Appelez l’API **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/GetAnalyzeLayoutResult)** (Obtenir le résultat de l’analyse de la disposition), à l’aide de l’ID d’opération obtenu à l’étape précédente.
1. Récupérez la réponse et écrivez le contenu dans un fichier. Pour chaque formulaire source, le fichier OCR correspondant doit avoir le nom de fichier d’origine suivi de `.ocr.json`. La sortie JSON OCR doit avoir le format suivant. Pour obtenir un exemple complet, consultez l’[exemple de fichier OCR](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json). 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>Créer les fichiers d’étiquette

Les fichiers d’étiquette contiennent des associations clé-valeur qu’un utilisateur a entrées manuellement. Elles sont nécessaires pour l’entraînement des données étiquetées, mais tous les fichiers sources ne doivent pas avoir un fichier d’étiquettes correspondant. Les fichiers sources sans étiquette sont traités comme des documents d’entraînement ordinaires. Nous vous recommandons d’utiliser au moins cinq fichiers étiquetés pour effectuer un entraînement fiable. Vous pouvez vous servir d’un outil à interface utilisateur comme l’[outil d’étiquetage des exemples](./label-tool.md) pour générer ces fichiers.

Quand vous créez un fichier d’étiquette, vous pouvez éventuellement spécifier des régions&mdash;positions exactes des valeurs dans le document. L’entraînement gagne ainsi en justesse. Les régions prennent la forme d’un ensemble de huit valeurs correspondant à quatre coordonnées X,Y : en haut à gauche, en haut à droite, en bas à droite et en bas à gauche. Les valeurs de coordonnée sont comprises entre zéro et un, et sont mises à l’échelle en fonction des dimensions de la page.

Pour chaque formulaire source, le fichier d’étiquette correspondant doit avoir le nom de fichier d’origine suivi de `.labels.json`. Le fichier d’étiquette doit avoir le format suivant. Pour obtenir un exemple complet, consultez l’[exemple de fichier d’étiquette](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json).

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> Vous ne pouvez appliquer qu’une seule étiquette à chaque élément de texte, et chaque étiquette ne peut être appliquée qu’une seule fois par page. Vous ne pouvez pas appliquer une étiquette dans plusieurs pages.


## <a name="train-a-model-using-labeled-data"></a>Entraîner un modèle à l’aide de données étiquetées

Pour entraîner un modèle avec des données étiquetées, appelez l’API **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/TrainCustomModelAsync)** (Entraîner un modèle personnalisé) en exécutant le code Python suivant. Avant d’exécuter le code, apportez les modifications suivantes :

1. Remplacez `<Endpoint>` par l’URL du point de terminaison pour votre ressource Form Recognizer.
1. Remplacez `<SAS URL>` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. Pour récupérer l’URL SAS, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Remplacez `<Blob folder name>` par le nom du dossier dans votre conteneur d’objets blob où se trouvent les données d’entrée. Ou, si vos données sont à la racine, laissez ce champ vide et supprimez le champ `"prefix"` du corps de la requête HTTP.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

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

Une fois le processus d’entraînement terminé, vous recevez une réponse `201 (Success)` avec du contenu JSON comme suit. La réponse a été raccourcie à des fins de simplicité.

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

Une fois le processus terminé, vous recevez une réponse `202 (Success)` avec du contenu JSON sous la forme suivante. La réponse a été raccourcie à des fins de simplicité. Les associations clé/valeur principales se trouvent dans le nœud `"documentResults"`. Les résultats de l’API Disposition (le contenu et les positions de tout le texte dans le document) se trouvent dans le nœud `"readResults"`.

```json
{ 
    "analyzeResult":{ 
      "version":"2.0.0",
      "readResults":[ 
        { 
          "page":1,
          "language":"en",
          "angle":0,
          "width":8.5,
          "height":11,
          "unit":"inch",
          "lines":[ 
            { 
              "language":"en",
              "boundingBox":[ 
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
          ]
        }
      ],
      "pageResults":[ 
        { 
          "page":1,
          "tables":[ 
            { 
              "rows":2,
              "columns":6,
              "cells":[ 
                { 
                  "rowIndex":0,
                  "columnIndex":0,
                  "text":"Invoice Number",
                  "boundingBox":[ 
                    0.5075,
                    2.8088,
                    1.9061,
                    2.8088,
                    1.9061,
                    3.3219,
                    0.5075,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/8/words/0",
                    "#/readResults/0/lines/8/words/1"
                  ]
                },
                ...        
              ]
            }
          ]
        }
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/15/words/0"
              ]
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/12/words/0"
              ]
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/16/words/0"
              ]
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Améliorer les résultats

Examinez les valeurs `"confidence"` pour chaque résultat de clé/valeur sous le nœud `"documentResults"`. Vous devez également examiner les scores de confiance dans le nœud `"readResults"`, qui correspondent à l’opération de disposition. La confiance des résultats de disposition n’affecte pas la confiance des résultats de l’extraction de clé/valeur. Vous devez donc vérifier les deux.
* Si les scores de confiance pour l’opération de disposition sont faibles, essayez d’améliorer la qualité de vos documents d’entrée (voir [Critères des entrées](../overview.md#input-requirements)).
* Si les scores de confiance pour l’opération d’extraction de clé/valeur sont faibles, assurez-vous que les documents en cours d’analyse sont du même type que les documents utilisés dans le jeu d’entraînement. Si les documents du jeu d’entraînement présentent des variantes d’apparence, envisagez de les diviser en différents dossiers et d’entraîner un modèle pour chaque variante.

### <a name="avoid-cluttered-labels"></a>Éviter les étiquettes encombrées

Parfois, quand vous appliquez des étiquettes différentes dans la même ligne de texte, le service peut fusionner ces étiquettes en un seul champ. Par exemple, dans une adresse, vous pouvez étiqueter la ville, l’État et le code postal sous forme de champs différents, mais pendant la prédiction, ces champs ne sont pas reconnus séparément.

Conscients que ce scénario est essentiel pour nos clients, nous travaillons à son amélioration. Nous recommandons à nos utilisateurs d’étiqueter plusieurs champs encombrés en un seul champ, puis de séparer les termes dans un post-traitement des résultats de l’extraction.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser l’API REST Form Recognizer avec Python pour entraîner un modèle avec des données étiquetées manuellement. Consultez à présent la documentation de référence sur l’API pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm)
