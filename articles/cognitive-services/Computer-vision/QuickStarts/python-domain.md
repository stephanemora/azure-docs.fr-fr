---
title: 'Démarrage rapide : Contenu spécifique à un domaine - REST, Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous utilisez un modèle de domaine pour identifier des célébrités et des monuments dans une image à l’aide de l’API Vision par ordinateur avec Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8e5ff917e7283457e7ff1d4c5fd0cbd91a1b0ace
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973745"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Démarrage rapide : Utiliser un modèle de domaine à l’aide de l’API REST et Python dans Vision par ordinateur

Dans ce guide de démarrage rapide, vous allez utiliser un modèle de domaine pour identifier des monuments, ou éventuellement des célébrités, dans une image stockée à distance à l’aide de l’API REST de Vision par ordinateur. Avec la méthode [Reconnaître le contenu de domaine spécifique](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), vous appliquez un modèle de domaine spécifique pour reconnaître le contenu dans une image.

Vous pouvez exécuter ce démarrage rapide étape par étape à l’aide d’un bloc-notes Jupyter sur [MyBinder](https://mybinder.org). Pour lancer Binder, sélectionnez le bouton suivant :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/try/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Si vous souhaitez exécuter l’exemple en local, [Python](https://www.python.org/downloads/) doit être installé.
- Vous devez disposer d’une clé d’abonnement pour la Vision par ordinateur. Vous pouvez obtenir une clé d’essai gratuit auprès de [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vous pouvez également suivre les instructions mentionnées dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Vision par ordinateur et obtenir votre clé. Ensuite, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la chaîne de point de terminaison de la clé et du service, nommés respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-landmarks-sample"></a>Créer et exécuter l’exemple des monuments

Pour créer et exécuter l’exemple des monuments, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur de texte.
1. Remplacez éventuellement la valeur de `image_url` par l’URL d’une autre image dans laquelle vous voulez détecter les monuments.
1. Enregistrez le code dans un fichier avec une extension `.py`. Par exemple : `get-landmarks.py`.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python get-landmarks.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
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

landmark_analyze_url = endpoint + "vision/v2.1/models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>Examiner la réponse pour l’exemple des monuments

Une réponse correcte est retournée au format JSON. La page Web d’exemple analyse et affiche une réponse correcte dans la fenêtre d’invite de commande, comme dans l’exemple suivant :

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>Créer et exécuter l’exemple des célébrités

Pour créer et exécuter l’exemple des monuments, effectuez les étapes suivantes :

1. Copiez le code ci-après dans un éditeur de texte.
1. Modifiez le code comme ci-dessous :
    1. Remplacez la valeur de `subscription_key` par votre clé d’abonnement.
    1. Si nécessaire, remplacez la valeur de `vision_base_url` par l’URL du point de terminaison de la ressource Vision par ordinateur dans la région Azure où vous avez obtenu vos clés d’abonnement.
    1. Remplacez éventuellement la valeur de `image_url` par l’URL d’une autre image dans laquelle vous voulez détecter les célébrités.
1. Enregistrez le code dans un fichier avec une extension `.py`. Par exemple : `get-celebrities.py`.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>Examiner la réponse pour l’exemple des célébrités

Une réponse correcte est retournée au format JSON. La page Web d’exemple analyse et affiche une réponse correcte dans la fenêtre d’invite de commande, comme dans l’exemple suivant :


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez les fichiers des deux exemples.

## <a name="next-steps"></a>Étapes suivantes

Explorez une application Python qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent, ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutoriel sur l’API Vision par ordinateur Python](../Tutorials/PythonTutorial.md)
