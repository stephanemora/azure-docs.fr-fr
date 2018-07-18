---
title: Appel et réponse - Démarrage rapide de Python pour l’API Recherche Web Bing dans Azure Cognitive Services | Microsoft Docs
description: Obtenez des informations et des exemples de code pour prendre rapidement en main l’API Recherche Web Bing dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368545"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Appel et réponse : votre première requête Recherche Web Bing dans Python

L’API Recherche Web Bing fournit une expérience similaire à Bing.com/Recherche en renvoyant les résultats de recherche identifiés par Bing comme pertinents pour la requête de l’utilisateur. Les résultats peuvent inclure des pages, images, vidéos, actualités et entités web, ainsi que des requêtes de recherche, corrections orthographiques, fuseaux horaires, conversions d’unités, traductions et calculs associés. Les types de résultats obtenus sont basés sur leur pertinence et le niveau des API Recherche Bing auxquelles vous êtes abonné.

Reportez-vous aux [informations de référence sur l’API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) pour obtenir des détails techniques sur les API.

Vous pouvez exécuter cet exemple comme un bloc-notes Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder : 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>Prérequis
Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les **API Recherche Bing**. Vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) pour suivre ce démarrage rapide. Vous avez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou d’une clé d’un abonnement payant de votre tableau de bord Azure.

## <a name="running-the-walkthrough"></a>Exécution de la procédure pas à pas

Définissez `subscription_key` en tant que clé API pour le service API Bing.


```python
subscription_key = None
assert subscription_key
```

Ensuite, vérifiez que le point de terminaison `search_url` est correct. Pour l’instant, un seul point de terminaison est utilisé pour les API Recherche Bing. Si vous rencontrez des erreurs d’autorisation, vérifiez bien cette valeur par rapport au point de terminaison de recherche Bing dans votre tableau de bord Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Définissez `search_term` sur la requête Bing pour Microsoft Cognitive Services.


```python
search_term = "Microsoft Cognitive Services"
```

Le bloc suivant utilise la bibliothèque `requests` dans Python pour appeler les API Recherche Bing et renvoyer les résultats en tant qu’objet JSON. Notez que nous transmettons la clé API via le dictionnaire `headers` et le terme de recherche via le dictionnaire `params`. Pour afficher la liste complète des options qui peuvent être utilisées pour filtrer les résultats de la recherche, consultez la documentation sur [l’API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L’objet `search_results` contient les résultats de la recherche, ainsi que les métadonnées enrichies telles que les pages et les requêtes associées. Les lignes de code suivantes mettent en forme les premières pages renvoyées par la requête.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Didacticiel sur l’application à page unique Recherche Web Bing](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Voir aussi 

[What is Bing Web Search?](../overview.md) (Présentation de Recherche Web Bing)  
[Essayer](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Obtenir une clé d’accès d’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Informations de référence sur l’API Recherche Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
