---
title: 'Démarrage rapide : Détecter les visages dans une image - API Visage, Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez détecter les visages d’une image à l’aide de l’API Visage avec Python.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: nolachar
ms.openlocfilehash: 7e1185fed1892dbd2c60434cfceec555bf7b0bc5
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129208"
---
# <a name="quickstart-detect-faces-in-an-image-using-python"></a>Démarrage rapide : Détecter les visages dans une image à l’aide de Python

Dans ce guide de démarrage rapide, vous allez détecter les visages humains d’une image distante à l’aide du service Visage. Les visages détectés sont délimités par des rectangles. Le sexe et l’âge de chaque personne sont superposés à leur visage. Pour utiliser une image locale, consultez la syntaxe dans [Vision par ordinateur : Analyser une image locale avec Python](../../Computer-vision/QuickStarts/python-disk.md).

Vous pouvez suivre ce guide de démarrage rapide comme un bloc-notes Jupyter dans [MyBinder](https://mybinder.org). Pour lancer Binder, sélectionnez le bouton suivant :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une clé d’abonnement pour exécuter l’exemple. Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Détecter des visages dans une image

Utilisez la méthode [Visage - Détecter](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour détecter les visages dans une image et retourner les attributs du visage, notamment :

* ID du visage : ID unique utilisé dans plusieurs scénarios d’API Visage.
* Rectangle du visage : valeurs gauche, haut, largeur et hauteur indiquant l’emplacement du visage dans l’image.
* Points de repère : tableau des 27 points de repère du visage pointant vers les positions importantes des composants du visage.
* Les attributs du visage, notamment l’âge, le sexe, l’intensité du sourire, la posture de la tête et la pilosité faciale.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code suivant dans un nouveau fichier de script Python.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Remplacez la valeur `face_api_url` par l’emplacement où vous avez obtenu vos clés d’abonnement, le cas échéant.
1. Éventuellement, remplacez la valeur `image_url` par une autre image.
1. Exécutez le script.

### <a name="face---detect-request"></a>Requête Visage - Détecter

Le code suivant utilise la bibliothèque Python `requests` pour appeler l’API de détection des visages. Il renvoie les résultats sous la forme d’un objet JSON. La clé API est transmise via le dictionnaire `headers`. Les types de fonctionnalités à reconnaître sont transmises via le dictionnaire `params`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from matplotlib import patches
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
face_api_url = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect'

# Set image_url to the URL of an image that you want to analyze.
image_url = 'https://how-old.net/Images/faces2/main007.jpg'

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
    'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
}
data = {'url': image_url}
response = requests.post(face_api_url, params=params, headers=headers, json=data)
faces = response.json()

# Display the original image and overlay it with the face information.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.figure(figsize=(8, 8))
ax = plt.imshow(image, alpha=0.6)
for face in faces:
    fr = face["faceRectangle"]
    fa = face["faceAttributes"]
    origin = (fr["left"], fr["top"])
    p = patches.Rectangle(
        origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    plt.text(origin[0], origin[1], "%s, %d"%(fa["gender"].capitalize(), fa["age"]),
             fontsize=20, weight="bold", va="bottom")
_ = plt.axis("off")
```

### <a name="face---detect-response"></a>Réponse Visage - Détecter

Une réponse correcte est retournée au format JSON, par exemple :

```json
[
  {
    "faceId": "35102aa8-4263-4139-bfd6-185bb0f52d88",
    "faceRectangle": {
      "top": 208,
      "left": 228,
      "width": 91,
      "height": 91
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 4.3,
        "yaw": -0.3
      },
      "gender": "female",
      "age": 27,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.65
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.5
          },
          {
            "color": "black",
            "confidence": 0.34
          },
          {
            "color": "red",
            "confidence": 0.32
          },
          {
            "color": "gray",
            "confidence": 0.14
          },
          {
            "color": "other",
            "confidence": 0.03
          }
        ]
      }
    }
  },
  {
    "faceId": "42502166-31bb-4ac8-81c0-a7adcb3b3e70",
    "faceRectangle": {
      "top": 109,
      "left": 125,
      "width": 79,
      "height": 79
    },
    "faceAttributes": {
      "smile": 1,
      "headPose": {
        "pitch": 0,
        "roll": 1.7,
        "yaw": 2.1
      },
      "gender": "male",
      "age": 32,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.4
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 1,
        "neutral": 0,
        "sadness": 0,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.11
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.74
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.02,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "blond",
            "confidence": 0.94
          },
          {
            "color": "red",
            "confidence": 0.76
          },
          {
            "color": "gray",
            "confidence": 0.2
          },
          {
            "color": "other",
            "confidence": 0.03
          },
          {
            "color": "black",
            "confidence": 0.01
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Visage utilisées pour détecter les visages humains dans une image, délimiter les visages avec des rectangles et retourner des attributs tels que l’âge et le sexe.

> [!div class="nextstepaction"]
> [API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
