---
title: 'Démarrage rapide : Détecter et encadrer des visages dans une image avec le SDK Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez créer un script Python qui utilise l’API Visage pour détecter et encadrer des visages dans une image distante.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: sbowles
ms.openlocfilehash: 741dd18a3b8da5e44d77c24d46adb8d550322281
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603296"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Démarrage rapide : Créer un script Python pour détecter et encadrer des visages dans une image

Dans ce guide de démarrage rapide, vous allez créer un script Python qui utilise l’API Visage Azure, par le biais du SDK Python, pour détecter des visages humains dans une image distante. L’application affiche une image sélectionnée et trace un cadre autour de chaque visage détecté.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

- Clé d’abonnement à l’API Visage. Vous pouvez obtenir une clé d’abonnement d’essai gratuit à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Vous pouvez également suivre les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service API Visage et obtenir votre clé.
- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Outil [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-face-sdk"></a>Obtenir le SDK Visage

Installez le SDK Python Visage en ouvrant l’invite de commandes et en exécutant la commande suivante :

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>Détecter des visages dans une image

Créez un script Python nommé _FaceQuickstart.py_ et ajoutez le code suivant. Ce code gère la fonctionnalité de base de la détection des visages. Vous devez remplacer `<Subscription Key>` par la valeur de votre clé. Vous devrez aussi peut-être changer la valeur de `BASE_URL` pour utiliser l’identificateur de région approprié pour votre clé. (Pour connaître la liste des points de terminaison de toutes les régions, consultez les [documents API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)). Les clés d’abonnement d’essai gratuit sont générées dans la région **westus**. Si vous le souhaitez, définissez `img_url` avec l’URL d’une image que vous souhaitez utiliser.

Le script détecter les visages en appelant la méthode **cognitive_face.face.detect**, laquelle inclut dans un wrapper l’API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) et retourne une liste de visages.

```python
import cognitive_face as CF

# Replace with a valid subscription key (keeping the quotes in place).
KEY = '<Subscription Key>'
CF.Key.set(KEY)

# Replace with your regional Base URL
BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>Essayer l’application

Exécutez l’application avec la commande `python FaceQuickstart.py`. Vous devriez obtenir une réponse de texte semblable à la suivante dans la fenêtre de console :

```console
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

La sortie représente une liste de visages détectés. Chaque élément dans cette liste est une instance **dict** où `faceId` est un ID unique pour le visage détecté et où `faceRectangle` décrit la position du visage détecté. 

> [!NOTE]
> Les ID de visage expirent après 24 heures. Vous devez donc stocker explicitement les données de visage si vous souhaitez les conserver à long terme.

## <a name="draw-face-rectangles"></a>Tracer des rectangles autour des visages

À l’aide des coordonnées reçues avec la commande précédente, vous pouvez tracer des rectangles sur l’image pour délimiter visuellement chaque visage. Vous devez installer Pillow (`pip install pillow`) pour utiliser le module Image de Pillow. En haut de *FaceQuickstart.py*, ajoutez le code suivant :

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Ensuite, en bas du script, ajoutez le code suivant. Ce code crée une fonction simple qui analyse les coordonnées des rectangles et utilise Pillow pour tracer des rectangles sur l’image d’origine. Elle affiche ensuite l’image dans votre visionneuse d’images par défaut.

```python
# Convert width height to a point in a rectangle


def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))


# Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

# For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

# Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>Exécution de l'application

Vous pouvez être invité à sélectionner une visionneuse d’images par défaut. Une image similaire à la suivante doit ensuite s’afficher. Les données de rectangle doivent également apparaître dans la fenêtre de console.

![Jeune femme dont le visage est indiqué par un rectangle rouge](../images/face-rectangle-result.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert le processus de base pour l’utilisation du SDK Python de l’API Visage, et créé un script pour détecter et encadrer des visages dans une image. Explorez à présent l’utilisation du SDK Python dans un exemple plus complexe. Accédez à l’exemple Cognitive Face Python sur GitHub, clonez-le dans votre dossier de projet, puis suivez les instructions contenues dans le fichier _README.md_.

> [!div class="nextstepaction"]
> [Exemple Cognitive Face Python](https://github.com/Microsoft/Cognitive-Face-Python)
