---
title: Démarrage rapide du SDK Python Recherche d’images | Microsoft Docs
description: Configuration de l’application console du SDK pour la recherche d’images.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370420"
---
# <a name="image-search-sdk-python-quickstart"></a>Démarrage rapide du SDK Python Recherche d’images

Le SDK Recherche d’images Bing fournit les fonctionnalités de l’API REST nécessaires pour lancer des requêtes web et analyser les résultats. 

Le [code source des exemples du SDK Python Recherche d’images Bing](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) est disponible sur GitHub.

## <a name="application-dependencies"></a>Dépendances de l’application
Si ce n’est déjà fait, installez Python. Le SDK est compatible avec Python 2.7, 3.3, 3.4, 3.5 et 3.6.

Il est généralement conseillé d’utiliser un [environnement virtuel](https://docs.python.org/3/tutorial/venv.html) pour le développement Python. Installez et initialisez l’environnement virtuel avec le [module venv](https://pypi.python.org/pypi/virtualenv). Vous devez installer virtualenv pour Python 2.7.
```
python -m venv mytestenv
```
Installez les dépendances du SDK Recherche d’images Bing :
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Client Recherche d’images
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Ajoutez des importations :
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Créez une instance de `CognitiveServicesCredentials`, et instanciez le client :
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Recherchez des images grâce à une requête (Yosemite), filtrés pour les gifs animés et les aspects larges. Vérifiez le nombre de résultats et imprimez insightsToken, l’URL de la miniature et l’URL web du premier résultat.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Recherchez des images pour (Yosemite), filtrés pour les gifs animés et les aspects larges.  Vérifiez le nombre de résultats.  Affichez `insightsToken`, `thumbnail url` et `web url` du premier résultat.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Obtenez des résultats de tendance :
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>Étapes suivantes

[Exemples du SDK Python pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


