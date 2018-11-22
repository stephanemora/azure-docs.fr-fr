---
title: 'Démarrage rapide : Effectuer une recherche avec Python - API Recherche Web Bing'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez apprendre à effectuer votre premier appel vers l’API Recherche Web Bing en utilisant Python et vous allez recevoir une réponse JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: aahi
ms.openlocfilehash: 2f034bd60747282ac9724250ab260e06122def6f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160992"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Démarrage rapide : Utiliser Python pour appeler l’API Recherche Web Bing  

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel vers l’API Recherche Web Bing et recevoir une réponse JSON en moins de 10 minutes.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

Cet exemple est exécuté en tant que bloc-notes Jupyter sur [MyBinder](https://mybinder.org). Cliquez sur le badge de lancement Binder :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="define-variables"></a>Définir des variables

Remplacez la valeur `subscription_key` par une clé d’abonnement valide à partir de votre compte Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Déclarez le point de terminaison de l’API Recherche Web Bing. Si vous rencontrez des erreurs d’autorisation, vérifiez bien cette valeur par rapport au point de terminaison de recherche Bing dans votre tableau de bord Azure.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

N’hésitez pas à personnaliser la requête de recherche en remplaçant la valeur de `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Exécuter une requête

Ce bloc utilise la bibliothèque `requests` pour appeler l’API Recherche Web Bing et renvoyer les résultats en tant qu’objet JSON. La clé API est transmise au dictionnaire `headers`, tandis que les termes de recherche et les paramètres de requête sont transmis au dictionnaire `params`. Consultez la documentation [API Recherche Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) pour obtenir la liste complète des options et paramètres.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Mettre en forme et afficher la réponse

L’objet `search_results` inclut les résultats de la recherche, ainsi que les métadonnées telles que les pages et les requêtes associées. Ce code utilise la bibliothèque `IPython.display` pour mettre en forme et afficher la réponse dans votre navigateur.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Exemple de code sur GitHub

Si vous souhaitez exécuter ce code localement, l’[exemple complet est disponible sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel sur l’application à page unique Recherche Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
