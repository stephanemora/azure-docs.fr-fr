---
title: 'Tutoriel : Détecter et encadrer des visages dans une image - API Visage, Python'
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Visage avec le SDK Python pour détecter les visages humains d’une image.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6cc3ac25d2196c0275b445503b79b9ac06a791d3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127735"
---
# <a name="tutorial-detect-and-frame-faces-with-the-face-api-and-python"></a>Tutoriel : Détecter et encadrer des visages avec l’API Visage et Python 

Dans ce didacticiel, vous apprendrez à invoquer l’API Visage via le Kit de développement logiciel (SDK) Python pour détecter les visages humains d’une image.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Installez Python 2.7+ ou Python 3.5+.
- Installez pip.
- Pour installer le Kit de développement logiciel (SDK) Python pour l’API Visage, procédez comme suit :

```bash
pip install cognitive_face
```

- Obtenez une [clé d’abonnement](https://azure.microsoft.com/try/cognitive-services/) pour Microsoft Cognitive Services. Vous pouvez utiliser votre clé principale ou secondaire pour ce didacticiel. (Remarque : pour utiliser n’importe quelle API Visage, vous devez posséder une clé d’abonnement valide.)

## <a name="detect-a-face-in-an-image"></a>Détecter un visage dans une image

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

Voici un exemple de résultat. Il s’agit d’une `list` de visages détectés. Chaque élément dans cette liste est une instance `dict` où `faceId` est un ID unique pour le visage détecté, et `faceRectangle` décrit la position du visage détecté. Un ID de visage expire au bout de 24 heures.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Tracer des rectangles autour des visages

À l’aide des coordonnées json reçues avec la commande précédente, vous pouvez tracer des rectangles sur l’image pour délimiter visuellement chaque visage. Vous aurez besoin de `pip install Pillow` pour utiliser le module de création d’images `PIL`.  À son début, ajoutez ce qui suit :

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Ensuite, après `print(faces)`, ajoutez les éléments suivants dans votre script :

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name="further-exploration"></a>Exploration approfondie

Pour vous aider à découvrir davantage l’API Visage, ce didacticiel fournit un exemple d’interface graphique utilisateur. Pour l’exécuter, installez d’abord [wxPython](https://wxpython.org/pages/downloads/) puis exécutez les commandes ci-dessous.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris le processus de base d’utilisation de l’API Visage via l’invocation du Kit de développement logiciel (SDK) Python. Pour plus d’informations sur les API, veuillez consulter les guides et la [documentation sur les API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Rubriques connexes

- [Prise en main de l’API Visage dans CSharp](FaceAPIinCSharpTutorial.md)
- [Prise en main de l’API Visage dans Java pour Android](FaceAPIinJavaForAndroidTutorial.md)
