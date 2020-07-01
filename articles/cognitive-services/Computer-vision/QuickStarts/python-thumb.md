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
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 968f9f1d658e79fb5213355358b91e98c353b07f
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84985690"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Démarrage rapide : Générer une miniature à l’aide de l’API REST Vision par ordinateur et de Python

Dans ce guide de démarrage rapide, vous allez générer une miniature à partir d’une image en utilisant l’API REST de Vision par ordinateur. La méthode [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c) vous permet de spécifier la hauteur et la largeur souhaitées. Vision par ordinateur utilise ensuite le rognage intelligent pour identifier la zone d’intérêt et générer des coordonnées de rognage en fonction de cette région.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL du point de terminaison, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.
- Un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Création et exécution de l’exemple

Pour créer et exécuter l’exemple, copiez le code suivant dans l’éditeur de code. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.0/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Ensuite, effectuez les actions suivantes :

1. (Facultatif) Remplacez la valeur de `image_url` par l’URL de votre propre image.
1. Enregistrez le code dans un fichier avec une extension `.py`. Par exemple : `get-thumbnail.py`.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse est retournée sous forme de données binaires qui représentent les données image de la miniature. L’exemple doit afficher cette image. Si la requête échoue, la réponse s’affiche dans la fenêtre d’invite de commandes et doit contenir un code d’erreur.

## <a name="run-in-jupyter-optional"></a>Exécuter dans Jupyter (facultatif)

Vous pouvez exécuter ce guide de démarrage rapide étape par étape à l’aide d’un notebook Jupyter sur [MyBinder](https://mybinder.org). Pour lancer Binder, sélectionnez le bouton suivant :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Étapes suivantes

À présent, explorez une application Python qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créez des miniatures avec un rognage intelligent et enfin détectez, classez, étiquetez et décrivez les caractéristiques visuelles des images.

> [!div class="nextstepaction"]
> [Tutoriel sur l’API Vision par ordinateur Python](../Tutorials/PythonTutorial.md)

* Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).
