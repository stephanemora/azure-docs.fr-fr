---
title: Démarrage rapide Python pour Azure Cognitive Services, API Recherche de vidéos Bing | Microsoft Docs
description: Obtenez des informations et des exemples de code pour commencer rapidement à utiliser l’API Recherche de vidéos Bing dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: ce4356f05e69540bc3bc3241e2ec1751ff7a7276
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368481"
---
# <a name="quickstart-for-bing-video-search-api-with-python"></a>Démarrage rapide pour l’API Recherche de vidéos Bing avec Python

Cette procédure pas à pas vous montre comment utiliser l’API Recherche de vidéos Bing qui fait partie de Microsoft Cognitive Services sur Azure. Reportez-vous aux [informations de référence sur l’API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) pour obtenir des détails techniques sur les API.

Vous pouvez exécuter cet exemple comme un bloc-notes Jupyter Notebook sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder : 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Prérequis
Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les **API Recherche Bing**. Vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) pour suivre ce guide de démarrage rapide. Vous avez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou d’une clé d’un abonnement payant de votre tableau de bord Azure.

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

Le bloc suivant utilise la bibliothèque `requests` dans Python pour faire appel aux API Recherche Bing et retourner les résultats comme un objet JSON. Notez que nous transmettons la clé API via le dictionnaire `headers` et le terme recherché via le dictionnaire `params`. Pour afficher la liste complète des options qui peuvent être utilisées pour filtrer les résultats de recherche, consultez la documentation sur [l’API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


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
