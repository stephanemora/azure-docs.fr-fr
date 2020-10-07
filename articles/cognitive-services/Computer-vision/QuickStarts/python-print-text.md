---
title: 'Démarrage rapide : Extraire le texte imprimé - REST, Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous extrayez le texte imprimé d’une image en utilisant l’API Vision par ordinateur avec Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: f070c9069f80f4980465e9842c789374aa224d41
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87847820"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-python"></a>Démarrage rapide : Extraire du texte imprimé (OCR) à l’aide de l’API REST Vision par ordinateur et de Python

> [!NOTE]
> Si vous extrayez du texte en langue anglaise, envisagez d’utiliser la nouvelle [opération de lecture](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text). Un [Démarrage rapide de Python](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/python-hand-text) est disponible. 

Dans ce guide de démarrage rapide, vous allez extraire le texte imprimé d’une image avec la reconnaissance optique de caractères (OCR) en utilisant l’API REST de Vision par ordinateur. Avec la méthode [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), vous pouvez détecter le texte imprimé dans une image et extraire les caractères reconnus dans un flux de caractères exploitable automatiquement.

Vous pouvez exécuter ce démarrage rapide étape par étape à l’aide d’un bloc-notes Jupyter sur [MyBinder](https://mybinder.org). Pour lancer Binder, sélectionnez le bouton suivant :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)


## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Si vous souhaitez exécuter l’exemple en local, [Python](https://www.python.org/downloads/) doit être installé.
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL du point de terminaison, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Création et exécution de l’exemple

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur de texte.
1. Remplacez éventuellement la valeur de `image_url` par l’URL d’une autre image à partir de laquelle vous voulez extraire le texte imprimé.
1. Enregistrez le code dans un fichier avec une extension `.py`. Par exemple : `get-printed-text.py`.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python get-printed-text.py`.

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

ocr_url = endpoint + "vision/v3.0/ocr"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" + \
    "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'language': 'unk', 'detectOrientation': 'true'}
data = {'url': image_url}
response = requests.post(ocr_url, headers=headers, params=params, json=data)
response.raise_for_status()

analysis = response.json()

# Extract the word bounding boxes and text.
line_infos = [region["lines"] for region in analysis["regions"]]
word_infos = []
for line in line_infos:
    for word_metadata in line:
        for word_info in word_metadata["words"]:
            word_infos.append(word_info)
word_infos

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(5, 5))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image, alpha=0.5)
for word in word_infos:
    bbox = [int(num) for num in word["boundingBox"].split(",")]
    text = word["text"]
    origin = (bbox[0], bbox[1])
    patch = Rectangle(origin, bbox[2], bbox[3],
                      fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(origin[0], origin[1], text, fontsize=20, weight="bold", va="top")
plt.show()
plt.axis("off")
```

## <a name="upload-image-from-local-storage"></a>Charger une image à partir du stockage local

Si vous souhaitez analyser une image locale, définissez l’en-tête Content-Type sur application/octet-stream et définissez le corps de la requête sur un tableau d’octets plutôt que sur des données JSON.

```python
image_path = "<path-to-local-image-file>"
# Read the image into a byte array
image_data = open(image_path, "rb").read()
# Set Content-Type to octet-stream
headers = {'Ocp-Apim-Subscription-Key': subscription_key, 'Content-Type': 'application/octet-stream'}
# put the byte array into your post request
response = requests.post(ocr_url, headers=headers, params=params, data = image_data)
```


## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. La page Web d’exemple analyse et affiche une réponse correcte dans la fenêtre d’invite de commande, comme dans l’exemple suivant :

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

À présent, explorez une application Python qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créez des miniatures avec un rognage intelligent et enfin détectez, classez, étiquetez et décrivez les caractéristiques visuelles des images.

> [!div class="nextstepaction"]
> [Tutoriel sur l’API Vision par ordinateur Python](../Tutorials/PythonTutorial.md)

* Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
