---
title: 'Démarrage rapide : Générer une miniature - REST, Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une miniature d’une image en utilisant l’API Vision par ordinateur avec Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0f131c7363a608962910f2cf0e5794972bf6208
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648929"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>Démarrage rapide : Générer une miniature à l’aide de l’API REST et Python dans Vision par ordinateur

Dans ce guide de démarrage rapide, vous allez générer une miniature d’image à l’aide de l’API REST de Vision par ordinateur. La méthode [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) vous permet de spécifier la hauteur et la largeur souhaitées. Vision par ordinateur utilise ensuite le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage en fonction de cette région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/try/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’une clé d’abonnement pour la Vision par ordinateur. Pour obtenir une clé d’abonnement, consultez [Obtention de clés d’abonnement](../Vision-API-How-to-Topics/HowToSubscribe.md).
- Un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Création et exécution de l’exemple

Pour créer et exécuter l’exemple, copiez le code suivant dans l’éditeur de code. 

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
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Ensuite, effectuez les actions suivantes :
1. Remplacez la valeur de `subscription_key` par votre clé d’abonnement.
1. Si nécessaire, remplacez la valeur de `vision_base_url` par l’URL du point de terminaison de la ressource Vision par ordinateur dans la région Azure où vous avez obtenu vos clés d’abonnement.
1. Remplacez éventuellement la valeur de `image_url` par l’URL d’une autre image pour laquelle vous souhaitez générer une miniature.
1. Enregistrez le code dans un fichier avec une extension `.py`. Par exemple : `get-thumbnail.py`.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse est retournée sous forme de données binaires qui représentent les données image de la miniature. L’exemple doit afficher cette image. Si la requête échoue, la réponse s’affiche dans la fenêtre d’invite de commandes et doit contenir un code d’erreur.

## <a name="run-in-jupyter-optional"></a>Exécuter dans Jupyter (facultatif)

Vous pouvez exécuter ce guide de démarrage rapide étape par étape à l’aide d’un notebook Jupyter sur [MyBinder](https://mybinder.org). Pour lancer Binder, sélectionnez le bouton suivant :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Étapes suivantes

Obtenez des informations plus détaillées sur la fonctionnalité de génération de miniatures.

> [!div class="nextstepaction"]
> [Génération de miniatures](../concept-generating-thumbnails.md)
