---
title: 'Démarrage rapide : Envoyer des requêtes de recherche à l’aide de l’API REST pour l’API Recherche d’images Bing dans Python'
description: Dans ce démarrage rapide, vous envoyez des requêtes de recherche à l’API Recherche Bing pour obtenir une liste des images pertinentes à l’aide de Python.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "41936736"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Démarrage rapide : Envoyer des requêtes de recherche à l’aide de l’API REST et de Python

L’API Recherche d’images Bing offre une expérience similaire à celle de Bing.com/Images, en ce sens qu’elle permet d’envoyer une requête de recherche à Bing pour obtenir une liste d’images pertinentes.

Cette procédure pas à pas donne un exemple simple d’appel dans l’API Recherche d’images Bing, et de post-traitement de l’objet JSON en résultant. Pour plus d’informations, consultez la [documentation de la Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Vous pouvez exécuter cet exemple comme un bloc-notes Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder : 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>Exécution de la procédure pas à pas
Pour poursuivre la procédure pas à pas, affectez à `subscription_key` votre clé API pour le service API Bing.


```python
subscription_key = None
assert subscription_key
```

Vérifiez ensuite que le point de terminaison `search_url` est correct. Pour l’instant, un seul point de terminaison est utilisé pour les API Recherche Bing. Si vous rencontrez des erreurs d’autorisation, vérifiez bien cette valeur par rapport au point de terminaison de Recherche Bing dans votre tableau de bord Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Définissez `search_term` pour rechercher des images de chiots.


```python
search_term = "puppies"
```

Le bloc suivant utilise la bibliothèque `requests` dans Python pour faire appel aux API Recherche Bing et retourner les résultats en tant qu’objet JSON. Notez que nous transmettons la clé API par le biais du dictionnaire `headers`, et le terme recherché par celui du dictionnaire `params`. Pour afficher la liste complète des options pouvant être utilisées pour filtrer les résultats de recherche, consultez la documentation sur [l’API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L’objet `search_results` contient les images réelles ainsi que les métadonnées enrichies, telles que les éléments associés. Par exemple, la ligne de code suivante peut extraire les URL des miniatures pour les 16 premiers résultats.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Ensuite, nous pouvons utiliser la bibliothèque `PIL` pour télécharger les images miniatures et la bibliothèque `matplotlib` pour les afficher sur une grille de $4 \times 4$.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel d’une application monopage Recherche d’images Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Voir aussi 

[Vue d’ensemble de la Recherche d’images Bing](../overview.md)  
[Essayer](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Obtenir une clé d’accès d’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
