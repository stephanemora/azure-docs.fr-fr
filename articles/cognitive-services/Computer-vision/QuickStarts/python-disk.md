---
title: 'API Vision par ordinateur avec Python - Démarrage rapide : analyser une image locale | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Dans ce démarrage rapide, vous analysez une image locale à l’aide de l’API Vision par ordinateur avec Python dans Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a1f3fce5a547f143f7c4884c6642e78f53d160e9
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770094"
---
# <a name="quickstart-analyze-a-local-image---rest-python"></a>Démarrage rapide : Analyser une image locale - REST, Python

Dans ce démarrage rapide, vous analysez une image locale à l’aide de l’API Vision par ordinateur. Pour analyser une image à distance, consultez [Analyser une image à distance avec Python](python-analyze.md).

Vous pouvez exécuter ce démarrage rapide étape par étape à l’aide d’un bloc-notes Jupyter sur [MyBinder](https://mybinder.org). Pour lancer Binder, sélectionnez le bouton suivant :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’API Vision par ordinateur, vous avez besoin d’une clé d’abonnement. Consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-local-image"></a>Analyser une image locale

Cet exemple est similaire à [Analyser une image à distance avec Python](python-analyze.md), excepté que l’image à analyser est lue localement à partir du disque. Deux modifications sont nécessaires :

- Ajouter un en-tête `{"Content-Type": "application/octet-stream"}` à la requête.
- Ajoutez les données d’image (tableau d’octets) au corps de la requête.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code suivant dans un nouveau fichier de script Python.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez la valeur `vision_base_url` par l’emplacement où vous avez obtenu vos clés d’abonnement, si nécessaire.
1. Remplacez la valeur `image_path` par le chemin d’accès à une image locale.
1. Exécutez le script.

Le code suivant utilise la bibliothèque `requests` Python pour appeler l’API Vision par ordinateur Analyser l’image. Il retourne les résultats sous la forme d’un objet JSON. La clé API est transmise via le dictionnaire `headers`. Les types de fonctionnalités à reconnaître sont transmises via le dictionnaire `params`. Les données d’image binaire sont transmises via le paramètre `data` à `requests.post`.

## <a name="analyze-image-request"></a>Requête Analyser l’image

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_path to the local path of an image that you want to analyze.
image_path = "C:/Documents/ImageToAnalyze.jpg"

# Read the image into a byte array
image_data = open(image_path, "rb").read()
headers    = {'Ocp-Apim-Subscription-Key': subscription_key,
              'Content-Type': 'application/octet-stream'}
params     = {'visualFeatures': 'Categories,Description,Color'}
response = requests.post(
    analyze_url, headers=headers, params=params, data=image_data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(image_data))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="analyze-image-response"></a>Réponse Analyser l’image

Une réponse de réussite est retournée au format JSON, par exemple :

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Python qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement les API Vision par ordinateur, essayez la [console de test d’API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutoriel sur l’API Vision par ordinateur Python](../Tutorials/PythonTutorial.md)
