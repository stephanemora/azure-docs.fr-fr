---
title: 'Démarrage rapide : Recherche de vidéos Bing, Python'
titlesuffix: Azure Cognitive Services
description: Procurez-vous des informations et des exemples de code pour commencer rapidement à utiliser l’API Recherche de vidéos Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: ccc27481289ffc686e3e480685ba421c762e3718
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161075"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>Démarrage rapide : API Recherche de vidéos Bing avec Python

Cette procédure pas à pas vous montre comment utiliser l’API Recherche de vidéos Bing qui fait partie de Microsoft Cognitive Services sur Azure. Reportez-vous aux [informations de référence sur l’API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) pour obtenir des détails techniques sur les API.

Vous pouvez exécuter cet exemple comme un bloc-notes Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder : 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Prérequis
Vous devrez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les **API Recherche Bing**. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) est suffisant pour suivre ce guide de démarrage rapide. Vous avez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou d’une clé d’un abonnement payant de votre tableau de bord Azure.

## <a name="running-the-walkthrough"></a>Exécution de la procédure pas à pas

Tout d’abord, affectez à `subscription_key` votre clé API pour le service API Bing.


```python
subscription_key = None
assert subscription_key
```

Vérifiez ensuite que le point de terminaison `search_url` est correct. Pour l’instant, un seul point de terminaison est utilisé pour les API Recherche Bing. Si vous rencontrez des erreurs d’autorisation, vérifiez bien cette valeur par rapport au point de terminaison de recherche Bing dans votre tableau de bord Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Définissez `search_term` pour rechercher des vidéos de chatons


```python
search_term = "kittens"
```

Le bloc suivant utilise la bibliothèque `requests` dans Python pour faire appel aux API Recherche Bing et retourner les résultats comme un objet JSON. Notez que nous transmettons la clé API par le biais du dictionnaire `headers`, et le terme recherché par celui du dictionnaire `params`. Pour afficher la liste complète des options qui peuvent être utilisées pour filtrer les résultats de recherche, consultez la documentation sur [l’API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L’objet `search_results` contient les vidéos pertinentes, ainsi que des métadonnées enrichies. Pour voir l’une des vidéos, utilisez sa propriété `embedHtml` et insérez-la dans un `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vidéos de pagination](paging-videos.md)
> [Redimensionnement et rognage des images miniatures](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Voir aussi 

 [Recherche de vidéos sur le web](search-the-web.md) [Essayer](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
