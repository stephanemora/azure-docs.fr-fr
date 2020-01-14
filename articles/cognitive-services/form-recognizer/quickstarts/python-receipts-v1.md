---
title: 'Démarrage rapide : Extraire des données de reçu à l’aide de Python – Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide montre comment utiliser l’API REST Form Recognizer avec Python pour extraire des données d’images de reçus.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5d95527fa5333b113fb53840fd687313a139413a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450920"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Démarrage rapide : Extraire des données de reçu à l’aide de l’API REST Form Recognizer avec Python

Ce guide de démarrage rapide permet d’utiliser l’API REST Azure Form Recognizer avec Python pour extraire et identifier des informations pertinentes de reçus.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise l’API Form Recognizer version 1.0. Si votre abonnement se trouve dans la région `West US 2` ou `West Europe`, vous devez suivre le [guide de démarrage rapide de l’API version 2.0](./python-train-extract.md).

## <a name="prerequisites"></a>Conditions préalables requises
Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :
- Accès à la préversion à accès limité de Form Recognizer. Pour accéder à la préversion, remplissez et envoyez le formulaire de [demande d’accès Form Recognizer](https://aka.ms/FormRecognizerRequestAccess).
- [Python](https://www.python.org/downloads/) doit être installé (si vous souhaitez exécuter l’exemple en local).
- Une URL pour une image de reçu. Pour ce guide de démarrage rapide, vous pouvez utiliser un [exemple d’image](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true).

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analyser un reçu

Pour commencer à analyser un reçu, vous appelez l’API **Analyser le reçu** en utilisant le script Python ci-dessous. Avant d’exécuter le script, apportez les modifications suivantes :

1. Remplacez `<Endpoint>` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `<your receipt URL>` par l’adresse URL d’une image de reçu.
1. Remplacez `<subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Enregistrez le code dans un fichier avec une extension .py. Par exemple, *form-recognizer-receipts.py*.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognizer-receipts.py`.

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location** que le script affiche sur la console. Cet en-tête contient un ID d’opération que vous pouvez utiliser pour interroger l’état de l’opération et obtenir les résultats d’analyse. Dans l’exemple de valeur suivant, la chaîne après `operations/` est l’ID d’opération.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Obtenir les résultats du reçu

Après avoir appelé l’API **Analyser le reçu**, vous appelez l’API **Obtenir le résultat du reçu** pour obtenir l’état de l’opération et les données extraites. Ajoutez le code suivant au bas de votre script Python. Cela a pour effet d’extraire la valeur d’ID d’opération et de la transmettre à un nouvel appel d’API. L’opération étant asynchrone, ce script appelle l’API à intervalles réguliers jusqu’à ce que les résultats soient disponibles. Nous recommandons un intervalle d’au moins une seconde.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Enregistrez le script.
1. Une fois encore, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Examiner la réponse

Le script affiche des réponses sur la console jusqu’à la fin de l’opération d’analyse. Ensuite, il imprime les données textuelles extraites au format JSON. Le champ `"recognitionResults"` contient toutes les lignes de texte extraites du reçu, et le champ `"understandingResults"` des informations de clé/valeur pour les parties les plus pertinentes du reçu.

Regardez l’image de reçu suivante et sa sortie JSON correspondante. La sortie a été raccourcie pour une meilleure lisibilité.

![Reçu d’un magasin Contoso](../media/contoso-receipt.png)

Le nœud `"recognitionResults"` contient tout le texte reconnu. Le texte est organisé par page, puis par ligne, puis par mots individuels. Le nœud `"understandingResults"` contient les valeurs spécifiques du reçu découvertes par le modèle. C’est là que vous trouverez des paires clé/valeur utiles, telles que la taxe, le total et l’adresse du commerçant.

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé les API REST Form Recognizer avec Python pour entraîner un modèle et l’exécuter dans un exemple de scénario. Consultez à présent la documentation de référence pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)