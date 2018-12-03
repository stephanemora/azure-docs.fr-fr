---
title: 'Démarrage rapide : Effectuer une recherche d’image avec Python - API Recherche d’images Bing'
description: Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’images Bing et recevoir une réponse JSON. Cette application Python simple envoie une requête de recherche à l’API et affiche les résultats bruts.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: a51935300238235338ec30ec7061101745b0e0e5
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314386"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Démarrage rapide : Envoyer des requêtes de recherche à l’aide de l’API REST et de Python

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’images Bing et recevoir une réponse JSON. Cette application Python simple envoie une requête de recherche à l’API et affiche les résultats bruts.

Alors que cette application est écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Vous pouvez exécuter cet exemple comme un bloc-notes Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


De plus, le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="running-the-quickstart"></a>Exécution du guide démarrage rapide

Pour commencer, définissez `subscription_key` sur une clé d’abonnement valide pour le service API Bing.

```python
subscription_key = None
assert subscription_key
```

Ensuite, vérifiez que le point de terminaison `search_url` est correct. Pour l’instant, un seul point de terminaison est utilisé pour les API Recherche Bing. Si vous rencontrez des erreurs d’autorisation, vérifiez bien cette valeur par rapport au point de terminaison de Recherche Bing dans votre tableau de bord Azure.


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

Utilisez ensuite la bibliothèque `PIL` pour télécharger les images miniatures et la bibliothèque `matplotlib` pour les afficher sur une grille de 4 sur 4.


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
plt.show()
```

## <a name="sample-json-response"></a>Exemple de réponse JSON

Les réponses de l’API Recherche d’images Bing sont retournées au format JSON. Cet exemple de réponse a été tronqué pour afficher un résultat unique.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel d’une application monopage Recherche d’images Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que la Recherche d’images Bing ?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Essayez une démonstration interactive en ligne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenir une clé d’accès Cognitive Services gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentation Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
