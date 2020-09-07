---
title: 'Démarrage rapide : Extraire des données de cartes de visite en utilisant Python – Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez utiliser l’API REST Form Recognizer avec Python pour extraire des données d’images de cartes de visites rédigées en anglais.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 5e27aaebc015f47e0fcdb5da81770d49b86ad000
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934325"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Démarrage rapide : Extraire des données de cartes de visite à l’aide de l’API REST Form Recognizer avec Python

Dans ce démarrage rapide, vous allez utiliser l’API REST Azure Form Recognizer avec Python pour extraire et identifier des informations pertinentes sur des cartes de visite.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :
- [Python](https://www.python.org/downloads/) doit être installé (si vous souhaitez exécuter l’exemple en local).
- Image d’une carte de visite. Pour ce guide de démarrage rapide, vous pouvez utiliser un [exemple d’image](../media/business-card-english.jpg).

> [!NOTE]
> Ce démarrage rapide utilise un fichier local. Pour utiliser une image de carte de visite distante accessible par URL, consultez la [documentation de référence](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analyser une carte de visite

Pour commencer à analyser une carte de visite, appelez l’API **[Analyze Business Card](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)** (Analyser la carte de visite) en utilisant le script Python ci-dessous. Avant d’exécuter le script, apportez les modifications suivantes :

1. Remplacez `<endpoint>` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `<path to your business card>` par le chemin local de votre image ou PDF de carte de visite.
1. Remplacez `<subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `<file type>` par « image/jpeg », « image/png », « application/pdf » ou « image/tiff ».

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Enregistrez le code dans un fichier avec une extension .py. Par exemple, *form-recognizer-businesscards.py*.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognizer-businesscards.py`.

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location** que le script affiche sur la console. L’en-tête contient un ID de résultat que vous pouvez utiliser pour interroger l’état de l’opération durable et obtenir les résultats. Dans l’exemple de valeur suivant, la chaîne après `operations/` est l’ID de résultat.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Obtenir les résultats de la carte de visite

Après avoir appelé l’API **Analyze Business Card** (Analyser la carte de visite), appelez l’API **[Get Analyze Business Card Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult)** (Obtenir le résultat de l’analyse de la carte de visite) pour obtenir l’état de l’opération et les données extraites. Ajoutez le code suivant au bas de votre script Python. Ce code utilise la valeur de l’ID de résultat dans un nouvel appel d’API. Ce script appelle l’API à intervalles réguliers jusqu’à ce que les résultats soient disponibles. Nous recommandons un intervalle d’au moins une seconde.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
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
1. Une fois encore, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Examiner la réponse
![Carte de visite de la société Contoso](../media/business-card-english.jpg)

Cet exemple illustre la sortie JSON retournée par Form Recognizer. Elle a été tronquée dans un souci de lisibilité.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Le script imprime les réponses dans la console tant que l’opération **Analyze Business Card** n’est pas terminée. Le nœud `"readResults"` contient tout le texte reconnu. Le texte est organisé par page, puis par ligne, puis par mots individuels. Le nœud `"documentResults"` contient les valeurs spécifiques de la carte de visite découvertes par le modèle. C’est là que vous trouverez des informations de contact utiles comme le nom de la société, le prénom, le nom, le numéro de téléphone, etc.


## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé l’API REST Form Recognizer avec Python pour extraire le contenu d’une carte de visite. Consultez à présent la documentation de référence pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
