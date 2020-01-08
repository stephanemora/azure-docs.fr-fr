---
title: 'Démarrage rapide : Effectuer une recherche d’actualités à l’aide de Python et l’API REST Recherche d’actualités Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour envoyer une requête à l’API REST Recherche d’actualités Bing à l’aide de Python et recevez une réponse JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1c424c75a4df193ec412355607c68abeda0560a5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448495"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Démarrage rapide : Effectuer une recherche d’actualités à l’aide de Python et l’API REST Recherche d’actualités Bing

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’actualités Bing et recevoir une réponse JSON. Cette application JavaScript simple envoie une requête de recherche à l’API et traite les résultats. Cette application est écrite en Python, mais l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Vous pouvez exécuter cet exemple de code comme un notebook Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder : 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Le code source de cet exemple est également disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis importez le module de requête. Créez des variables pour votre clé d’abonnement, le point de terminaison et un terme de recherche. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Créer les paramètres de la requête

1. Ajoutez votre clé d’abonnement à un nouveau dictionnaire en utilisant `"Ocp-Apim-Subscription-Key"` comme clé. Faites de même pour vos paramètres de recherche.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Envoyer une requête et obtenir une réponse

1. Utilisez la bibliothèque de requêtes pour appeler l’API Recherche visuelle Bing à l’aide de votre clé d’abonnement et des objets de dictionnaire créés à l’étape précédente.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` présente la réponse de l’API en tant qu’objet JSON. Accédez aux descriptions des articles contenus dans la réponse.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Affichage des résultats

Ces descriptions peuvent ensuite être restituées sous la forme d’un tableau, avec le mot clé de recherche surligné en **gras**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-news-search-single-page-app.md)
