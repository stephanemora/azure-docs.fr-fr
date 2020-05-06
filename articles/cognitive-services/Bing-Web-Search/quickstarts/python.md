---
title: 'Démarrage rapide : Effectuer une recherche avec Python - API Recherche Web Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour envoyer des requêtes à l'API REST Recherche Web Bing à l'aide de Python et recevoir une réponse JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c1af1142faca76cc58b6b3ca9a7106bc0433ea18
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74976364"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Démarrage rapide : Utiliser Python pour appeler l’API Recherche Web Bing  

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche Web Bing et recevoir la réponse JSON. Cette application Python envoie une requête de recherche à l’API et affiche la réponse. Alors que cette application est écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Cet exemple est exécuté en tant que bloc-notes Jupyter sur [MyBinder](https://mybinder.org). Sélectionnez le badge de lancement Binder :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Conditions préalables requises

* [Python 2.x ou 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Définir des variables

Remplacez la valeur `subscription_key` par une clé d’abonnement valide à partir de votre compte Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Déclarez le point de terminaison de l’API Recherche Web Bing. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

N’hésitez pas à personnaliser la requête de recherche en remplaçant la valeur de `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Exécuter une requête

Ce bloc utilise la bibliothèque `requests` pour appeler l’API Recherche Web Bing et renvoyer les résultats en tant qu’objet JSON. La clé API est transmise au dictionnaire `headers`, tandis que les termes de recherche et les paramètres de requête sont transmis au dictionnaire `params`. Consultez la documentation [API Recherche Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) pour obtenir la liste complète des options et paramètres.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Mettre en forme et afficher la réponse

L’objet `search_results` inclut les résultats de la recherche, ainsi que les métadonnées comme les pages et les requêtes associées. Ce code utilise la bibliothèque `IPython.display` pour mettre en forme et afficher la réponse dans votre navigateur.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Exemple de code sur GitHub

Si vous souhaitez exécuter ce code localement, l’[exemple complet est disponible sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel sur l’application à page unique Recherche Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
