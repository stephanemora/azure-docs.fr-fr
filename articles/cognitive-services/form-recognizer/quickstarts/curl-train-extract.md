---
title: 'Démarrage rapide : Entraîner un modèle et extraire des données de formulaire à l’aide de cURL - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous utilisez l’API REST Form Recognizer avec cURL pour entraîner un modèle et extraire des données à partir de formulaires.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: fd240abee3bb19b3c54650756a3329d4d1ef8ae5
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84113519"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec cURL

Dans ce guide de démarrage rapide, vous utilisez l’API REST Azure Form Recognizer avec cURL pour entraîner et scorer des formulaires afin d’extraire des paires clé-valeur et des tables.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :
- [cURL](https://curl.haxx.se/windows/) installé.
- Au minimum un ensemble de six formulaires du même type. Vous en utiliserez cinq pour entraîner le modèle, puis vous testerez le modèle avec le sixième formulaire. Vos formulaires peuvent être de différents types de fichiers, mais ils doivent être du même type de document. Vous pouvez utiliser un [exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) pour ce guide de démarrage rapide. Chargez les fichiers d’entraînement à la racine d’un conteneur de stockage d’objets blob dans un compte Stockage Azure. Vous pouvez placer les fichiers de test dans un dossier distinct.

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Entraîner un modèle Form Recognizer

Avant toute chose, vous avez besoin d’un jeu de données d’entraînement dans Azure Storage Blob. Vous devez disposer au minimum de cinq formulaires remplis (documents PDF et/ou images) d’un type ou d’une structure identique en tant que données d’entrée principales. Vous pouvez également utiliser un formulaire vide avec deux formulaires remplis. Le nom de fichier du formulaire vide doit inclure le mot « vide ». Consultez [Créer un jeu de données d’apprentissage pour un modèle personnalisé](../build-training-data-set.md) pour obtenir des conseils et des options pour constituer vos données d’apprentissage.

> [!NOTE]
> Vous pouvez utiliser la fonctionnalité de données étiquetées pour étiqueter manuellement une partie ou la totalité de vos données d’entraînement. Il s’agit d’un processus plus complexe, mais le modèle entraîné qui en résulte est de meilleure qualité. Consultez la section [Effectuer un entraînement avec des étiquettes](../overview.md#train-with-labels) de la vue d’ensemble pour en savoir plus sur cette fonctionnalité.

Pour entraîner un modèle Form Recognizer à l’aide des documents de votre conteneur d’objets blob Azure, appelez l’API **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** (Entraîner un modèle personnalisé) en exécutant la commande cURL ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `<Endpoint>` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `<subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `<SAS URL>` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. Pour récupérer l’URL SAS, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Vous recevrez une réponse `201 (Success)` avec un en-tête **Location**. La valeur de cet en-tête est l’ID du nouveau modèle en cours d’entraînement. 

## <a name="get-training-results"></a>Obtenir les résultats de l’entraînement

Une fois que vous avez démarré l’opération d’entraînement, vous utilisez une nouvelle opération, **[Obtenir un modèle personnalisé](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** , pour vérifier l’état de l’entraînement. Transmettez l’ID de modèle dans cet appel d’API pour vérifier l’état de l’entraînement :

1. Remplacez `<Endpoint>` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer.
1. Remplacez `<subscription key>` par votre clé d’abonnement.
1. Remplacez `<model ID>` par l’ID de modèle que vous avez reçu à l’étape précédente.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Vous recevrez une réponse `200 (Success)` avec un corps JSON sous la forme suivante. Notez le champ `"status"`. Il aura pour valeur `"ready"` une fois l’entraînement terminé. Si l’entraînement du modèle n’est pas terminé, vous devez réinterroger le service en réexécutant la commande. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

Le champ `"modelId"` contient l’ID du modèle que vous êtes en train d’entraîner. Vous en aurez besoin à l’étape suivante.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analyser des formulaires afin d’en extraire des tableaux et des paires clé-valeur

À présent, vous allez utiliser le modèle nouvellement entraîné pour analyser un document et en extraire des tableaux et des paires clé-valeur. Appelez l’API **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** (Analyser un formulaire) en exécutant la commande cURL suivante. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `<Endpoint>` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `<model ID>` par l’ID de modèle que vous avez reçu à la section précédente.
1. Remplacez `<SAS URL>` par une URL SAS vers votre fichier dans le stockage Azure. Suivez les étapes de la section Entraînement, mais au lieu d’obtenir une URL SAS pour tout le conteneur d’objets Blob, obtenez l’URL du fichier spécifique que vous souhaitez analyser.
1. Remplacez `<subscription key>` par votre clé d’abonnement.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Vous recevrez une réponse `202 (Success)` avec un en-tête **Operation-Location**. La valeur de cet en-tête inclut un ID de résultats à utiliser pour suivre les résultats de l’opération d’analyse. Enregistrez cet ID de résultats pour l’étape suivante.

## <a name="get-the-analyze-results"></a>Obtenir les résultats de l’analyse

Utilisez l’API suivante pour interroger les résultats de l’opération d’analyse.

1. Remplacez `<Endpoint>` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `<result ID>` par l’ID que vous avez reçu à la section précédente.
1. Remplacez `<subscription key>` par votre clé d’abonnement.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Vous recevrez une réponse `200 (Success)` avec un corps JSON sous la forme suivante. La sortie a été raccourcie à des fins de simplicité. Notez le champ `"status"` vers le bas. La valeur est `"succeeded"` quand l’opération d’analyse est terminée. Si l’opération d’analyse n’est pas terminée, vous devez réinterroger le service en réexécutant la commande. Nous vous recommandons d’attendre une seconde ou plus entre chaque appel.

Les tableaux et les associations clé/valeur principales se trouvent dans le nœud `"pageResults"`. Si vous avez également spécifié l’extraction de texte brut par le biais du paramètre d’URL *includeTextDetails*, le nœud `"readResults"` affiche le contenu et les positions de tout le texte dans le document.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Améliorer les résultats

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé les API REST Form Recognizer avec cURL pour entraîner un modèle et l’exécuter dans un exemple de scénario. Consultez à présent la documentation de référence pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
