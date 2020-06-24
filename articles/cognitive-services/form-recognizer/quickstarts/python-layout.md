---
title: 'Démarrage rapide : Extraire du texte et des informations de disposition à l’aide de Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous utilisez l’API REST Disposition de Form Recognizer avec Python pour lire du texte et des données de tableau à partir de vos formulaires.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: tracking-python
ms.openlocfilehash: 8893ebf1288e592131938f39b10e204f98471fa8
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208142"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Démarrage rapide : Extraire du texte et des informations de disposition à l’aide de l’API REST Form Recognizer avec Python

Ce guide de démarrage rapide permet d’utiliser l’API REST Azure Form Recognizer avec Python pour extraire des informations de disposition de texte et des données de tableau à partir de documents de formulaire.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :
- [Python](https://www.python.org/downloads/) doit être installé (si vous souhaitez exécuter l’exemple en local).
- Un document de formulaire. Vous pouvez télécharger une image à partir de l’[exemple de jeu de données ](https://go.microsoft.com/fwlink/?linkid=2090451) pour ce guide de démarrage rapide.

> [!NOTE]
> Ce guide de démarrage rapide utilise un document stocké localement. Pour savoir comment utiliser les fichiers distants accessibles par URL, consultez la [documentation de référence](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync).


## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analyser la disposition du formulaire

Pour commencer à analyser la disposition, vous appelez l’API **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeLayoutAsync)** (Analyser la disposition) en utilisant le script Python ci-dessous. Avant d’exécuter le script, apportez les modifications suivantes :

1. Remplacez `<Endpoint>` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `<path to your form>` par le chemin de votre document de formulaire local.
1. Remplacez `<subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Enregistrez le code dans un fichier avec une extension .py. Par exemple, *form-recognizer-layout.py*.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognizer-layout.py`.

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location** que le script affiche sur la console. Cet en-tête contient un ID d’opération que vous pouvez utiliser pour interroger l’état de l’opération asynchrone et obtenir les résultats. Dans l’exemple de valeur suivant, la chaîne après `operations/` est l’ID d’opération.

```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Obtenir les résultats de la disposition

Après avoir appelé l’API **Analyze Layout** (Analyser la disposition), vous appelez l’API **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/GetAnalyzeLayoutResult)** (Obtenir le résultat de l’analyse de la disposition) pour obtenir l’état de l’opération et les données extraites. Ajoutez le code suivant au bas de votre script Python. Ce code utilise la valeur de l’ID d’opération dans un nouvel appel d’API. Ce script appelle l’API à intervalles réguliers jusqu’à ce que les résultats soient disponibles. Nous recommandons un intervalle d’au moins une seconde.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Enregistrez le script.
1. Une fois encore, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Examiner la réponse

Le script affiche des réponses sur la console jusqu’à la fin de l’opération d’**analyse de la disposition**. Ensuite, il imprime les données extraites au format JSON. Le nœud `"readResults"` contient chaque ligne de texte avec sa position de cadre englobant respectif dans la page. Le champ `"pageResults"` affiche chaque morceau de texte dans les tableaux, chacun avec ses coordonnées de colonne et de ligne.

Regardez l’image de facture suivante et sa sortie JSON correspondante. La sortie a été raccourcie à des fins de simplicité.

> [!div class="mx-imgBorder"]
> ![Document de facture Contoso avec un tableau](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
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
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
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
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé l’API REST Form Recognizer avec Python pour extraire la disposition de texte d’une facture. Consultez à présent la documentation de référence pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeLayoutAsync)
