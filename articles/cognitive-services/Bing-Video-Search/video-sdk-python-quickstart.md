---
title: Démarrage rapide du Kit de développement logiciel (SDK) Python pour Recherche de vidéos | Microsoft Docs
description: Configuration de l’application console du Kit de développement logiciel (SDK) pour Recherche de vidéos.
titleSuffix: Azure Video Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/15/2018
ms.author: v-gedod
ms.openlocfilehash: 1c4769a6ca3391fa595cc078651beff330bbfd60
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370441"
---
# <a name="video-search-sdk-python-quickstart"></a>Démarrage rapide du Kit de développement logiciel (SDK) Python pour Recherche de vidéos

Le Kit de développement logiciel (SDK) pour Recherche de vidéos Bing fournit les fonctionnalités de l’API REST nécessaires pour lancer des requêtes web et analyser les résultats.

Le [code source des exemples du Kit de développement logiciel (SDK) Python pour Recherche de vidéos Bing](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) est disponible sur GitHub.


## <a name="application-dependencies"></a>Dépendances de l’application
Si ce n’est déjà fait, installez Python. Le Kit de développement logiciel (SDK) est compatible avec Python 2.7, 3.3, 3.4, 3.5 et 3.6.

Il est généralement conseillé d’utiliser un [environnement virtuel](https://docs.python.org/3/tutorial/venv.html) pour le développement Python. Installez et initialisez l’environnement virtuel avec le [module venv](https://pypi.python.org/pypi/virtualenv). Installez virtualenv pour Python 2.7.
```
python -m venv mytestenv
```
Installez les dépendances du Kit de développement logiciel (SDK) pour Recherche de vidéos Bing :
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```
## <a name="video-search-client"></a>Client Recherche de vidéos
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Ajoutez des importations :
```
subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Créez une instance de `CognitiveServicesCredentials`, et instanciez le client :
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Recherchez des vidéos pour (SwiftKey), puis vérifiez que le nombre de résultats. Affichez `ID`, `name` et `URL` du premier résultat de vidéo.
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

try:
    video_result = client.videos.search(query="SwiftKey")
    print("Search videos for query \"SwiftKey\"")

    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")

except Exception as err:
    print("Encountered exception. {}".format(err))

```
Recherchez des vidéos sur (Bellevue Trailer) qui sont gratuites, brèves et d’une résolution de 1080 p. Vérifiez le nombre de résultats et affichez `ID`, `name` et `URL` du premier résultat de vidéo.
```
def video_search_with_filtering(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(
            query="Bellevue Trailer",
            pricing=VideoPricing.free,  # Can use the str "free" too
            length=VideoLength.short,   # Can use the str "short" too
            resolution=VideoResolution.hd1080p  # Can use the str "hd1080p" too
        )
        print("Search videos for query \"Bellevue Trailer\" that is free, short and 1080p resolution")

        if video_result.value:
            first_video_result = video_result.value[0]
            print("Video result count: {}".format(len(video_result.value)))
            print("First video id: {}".format(first_video_result.video_id))
            print("First video name: {}".format(first_video_result.name))
            print("First video url: {}".format(first_video_result.content_url))
        else:
            print("Didn't see any video result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

Obtenez des résultats de tendance. Vérifiez les vignettes et catégories de bannière :
```
def video_trending(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_result = client.videos.trending()
        print("Search trending video")

        # Banner tiles
        if trending_result.banner_tiles:
            first_banner_tile = trending_result.banner_tiles[0]
            print("Banner tile count: {}".format(len(trending_result.banner_tiles)))
            print("First banner tile text: {}".format(first_banner_tile.query.text))
            print("First banner tile url: {}".format(first_banner_tile.query.web_search_url))
        else:
            print("Couldn't find banner tiles!")

        # Categorires
        if trending_result.categories:
            first_category = trending_result.categories[0]
            print("Category count: {}".format(len(trending_result.categories)))
            print("First category title: {}".format(first_category.title))
            if first_category.subcategories:
                first_subcategory = first_category.subcategories[0]
                print("Subcategory count: {}".format(len(first_category.subcategories)))
                print("First subcategory title: {}".format(first_subcategory.title))
                if first_subcategory.tiles:
                    first_tile = first_subcategory.tiles[0]
                    print("Subcategory tile count: {}".format(len(first_subcategory.tiles)))
                    print("First tile text: {}".format(first_tile.query.text))
                    print("First tile url: {}".format(first_tile.query.web_search_url))
                else:
                    print("Couldn't find subcategory tiles!")
            else:
                print("Couldn't find subcategories!")
        else:
            print("Couldn't find categories!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Recherchez des vidéos pour (Bellevue Trailer), puis recherchez les informations détaillées de la première vidéo.
```
def video_detail(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(query="Bellevue Trailer")
        first_video_result = video_result.value[0]

        video_details = client.videos.details(
            query="Bellevue Trailer",
            id=first_video_result.video_id,
            modules=[VideoInsightModule.all]  # Can use ["all"] too
        )
        print("Search detail for video id={}, name={}".format(
            first_video_result.video_id,
            first_video_result.name
        ))

        if video_details.video_result:
            print("Expected Video id: {}".format(video_details.video_result.video_id))
            print("Expected Video name: {}".format(video_details.video_result.name))
            print("Expected Video url: {}".format(video_details.video_result.content_url))
        else:
            print("Couldn't find expected video")

        if video_details.related_videos.value:
            first_related_video = video_details.related_videos.value[0]
            print("Related video count: {}".format(len(video_details.related_videos.value)))
            print("First related video id: {}".format(first_related_video.video_id))
            print("First related video name: {}".format(first_related_video.name))
            print("First related video content url: {}".format(first_related_video.content_url))
        else:
            print("Couldn't find any related video!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Étapes suivantes

[Exemples du Kit de développement logiciel (SDK) Python pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples).

