---
title: 'Démarrage rapide : Effectuer une recherche d’actualités - Kit de développement logiciel (SDK) Recherche d’actualités Bing pour Python'
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour rechercher des actualités à l’aide du Kit de développement logiciel (SDK) Recherche d’actualités Bing pour Python et traiter la réponse.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/14/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 3489a9634cecd776afc8619a81acd72a2649ec36
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261195"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Démarrage rapide : Effectuer une recherche d’actualités avec le Kit de développement logiciel (SDK) Recherche d’actualités Bing pour Python

Le Kit de développement logiciel (SDK) Recherche d’actualités fournit les fonctionnalités de l’API REST pour les requêtes web et l’analyse des résultats. 

Le [code source des exemples du Kit de développement logiciel (SDK) Python pour Recherche d’actualités Bing](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) est disponible sur GitHub.

## <a name="application-dependencies"></a>Dépendances de l’application
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous **Recherche**.  Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Si ce n’est déjà fait, installez Python. Le Kit de développement logiciel (SDK) est compatible avec Python 2.7, 3.3, 3.4, 3.5 et 3.6.

Il est généralement conseillé d’utiliser un [environnement virtuel](https://docs.python.org/3/tutorial/venv.html) pour le développement Python. Installez et initialisez l’environnement virtuel avec le [module venv](https://pypi.python.org/pypi/virtualenv). Vous devez installer virtualenv pour Python 2.7.
```
python -m venv mytestenv
```
Installez les dépendances du Kit de développement logiciel (SDK) pour Recherche d’actualités Bing :
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Client de recherche d’actualités
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Ajoutez des importations :
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Créez une instance de `CognitiveServicesCredentials`. Instanciez le client :
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Recherchez les résultats, puis affichez le premier résultat de page web :
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
Recherchez avec des filtres les actualités les plus récentes concernant « Artificial Intelligence » (Intelligence artificielle) avec les paramètres `freshness` et `sortBy`. Vérifiez le nombre de résultats et affichez `totalEstimatedMatches`, `name`, `url`, `description`, `published time`, et `name of provider` du premier résultat d’élément d’actualités.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Effectuez une recherche sécurisée dans la catégorie actualités des films et divertissements TV. Vérifiez le nombre de résultats et affichez `category`, `name`, `url`, `description`, `published time`, et `name of provider` du premier résultat d’élément d’actualités.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Recherchez des rubriques d’actualités tendance dans Bing.  Vérifiez le nombre de résultats et affichez `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` et `image Url` du premier résultat d’actualités.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>Étapes suivantes

[Exemples du SDK Python pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


