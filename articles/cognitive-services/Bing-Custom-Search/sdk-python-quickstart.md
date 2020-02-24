---
title: 'Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec le kit SDK Python'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche depuis votre instance Recherche personnalisée Bing avec le SDK Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 02/12/2020
ms.author: aahi
ms.openlocfilehash: 4f4b6bad38992a9e0146d6324bc3a3fc7632ded2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201252"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec le kit SDK Python 

Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche depuis votre instance Recherche personnalisée Bing, avec le kit SDK Python. Si d’un côté la Recherche personnalisée Bing dispose d’une API REST compatible avec la plupart des langages de programmation, de l’autre, le kit SDK Recherche personnalisée Bing offre un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) avec une gestion des erreurs et des annotations supplémentaires.

## <a name="prerequisites"></a>Prérequis

- Une instance Recherche personnalisée Bing. Consultez [Démarrage rapide : Créer votre première instance Recherche personnalisée Bing](quick-start.md) pour plus amples informations.
- Python[ 2.x ou 3.x](https://www.python.org/) 
- [SDK Recherche de vidéos Bing pour Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Installer le Kit de développement logiciel (SDK) Python

Installez le Kit de développement logiciel (SDK) Recherche personnalisée Bing à l’aide de la commande suivante.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Créer une application

Créez un fichier Python dans votre éditeur ou IDE favori, puis ajoutez les importations suivantes.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Créer un client de recherche et envoyer une requête

1. Créez une variable pour votre clé d’abonnement et votre point de terminaison.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Créez une instance de `CustomSearchClient` utilisant un objet `CognitiveServicesCredentials` à l’aide de la clé d’abonnement. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Envoyez une requête de recherche avec `client.custom_instance.search()`. Ajoutez votre terme de recherche au paramètre `query`, et définissez `custom_config` sur votre ID de configuration personnalisée pour utiliser votre instance de recherche. Vous pouvez obtenir votre identifiant sur le portail [Recherche personnalisée Bing](https://www.customsearch.ai/), en cliquant sur l'onglet **Production**.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Afficher les résultats de la recherche

Si la requête renvoie des résultats de recherche sur une page web, obtenez le premier et imprimez son nom, son URL et le nombre total de pages web trouvées.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Recherche personnalisée](./tutorials/custom-search-web-page.md)
