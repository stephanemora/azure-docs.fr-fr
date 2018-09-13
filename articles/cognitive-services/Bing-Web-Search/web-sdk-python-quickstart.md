---
title: 'Démarrage rapide : Utiliser le kit de développement logiciel de l’API Recherche Web Bing pour Python'
description: Apprenez à utiliser le kit de développement logiciel de l’API Recherche Web Bing pour Python.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: ff8dc93693a5aec7b6efa3aefd05de8c90f517ed
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43186811"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Démarrage rapide : Utiliser le kit de développement logiciel de l’API Recherche Web Bing pour Python

Le kit de développement logiciel de l’API Recherche Web Bing simplifie l’intégration de l’API dans votre application Python. Dans ce démarrage rapide, vous allez apprendre à envoyer une requête, recevoir une réponse JSON et filtrer et analyser les résultats.

Vous voulez voir le code tout de suite ? Des [exemples de kit de développement logiciel de l’API Recherche Web Bing pour Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) sont disponibles sur GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Prérequis

Le Kit de développement logiciel de l’API Recherche Web Bing est compatible avec Python 2.7, 3.3, 3.4, 3.5 et 3.6. Nous vous recommandons d’utiliser un environnement virtuel pour ce démarrage rapide.

* Python 2.7, 3.3, 3.4, 3.5 ou 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) pour Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) pour Python 3.x

## <a name="create-and-configure-your-virtual-environment"></a>Créer et configurer votre environnement virtuel

Les instructions pour installer et configurer un environnement virtuel sont différentes pour Python 2.x et Python 3.x. Suivez les étapes ci-dessous pour créer et initialiser votre environnement virtuel.

### <a name="python-2x"></a>Python 2.x

Créer un environnement virtuel avec `virtualenv` pour Python 2.7 :

```console
virtualenv mytestenv
```

Activez votre environnement :

```console
cd mytestenv
source bin/activate
```

Installez les dépendances du SDK Recherche Web Bing :

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Créer un environnement virtuel avec `venv` pour Python 3.x :

```console
python -m venv mytestenv
```

Installez les dépendances du SDK Recherche Web Bing :

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Créer un client et imprimer vos premiers résultats

Maintenant que vous avez configuré votre environnement virtuel et installé les dépendances, nous allons créer un client. Le client gérera les requêtes pour l’API Recherche Web Bing et ses réponses.

Si la réponse contient des pages web, des images, des actualités ou des vidéos, le premier résultat de toutes ces catégories est imprimé.

1. Créez un projet Python dans votre IDE ou votre éditeur favori.
2. Copiez cet exemple de code dans votre projet :  
    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```
3. Remplacez `subscription_key` par une clé d’abonnement valide.
4. Exécutez le programme. Par exemple : `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Définir des fonctions et filtrer des résultats

Maintenant que vous avez effectué votre premier appel à l’API Recherche Web Bing, examinons quelques fonctions qui mettent en évidence les fonctionnalités du kit de développement logiciel pour affiner des requêtes et filtrer les résultats. Chaque fonction peut être ajoutée à votre programme Python créé dans la section précédente.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limiter le nombre de résultats retournés par Bing

Cet exemple utilise les paramètres `count` et `offset` pour limiter le nombre de résultats retournés à l’aide de la [`search`méthode](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) du kit de développement logiciel. Le `name` et `URL` pour le premier résultat sont imprimés.

1. Ajoutez ce code à votre projet Python :
    ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```
2. Exécutez le programme.

### <a name="filter-for-news-and-freshness"></a>Filtrer les actualités et l’actualisation

Cet exemple utilise les paramètres `response_filter` et `freshness` pour filtrer des résultats de recherche à l’aide de la [`search`méthode](https://docs.microsoft.com//api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) du kit de développement logiciel. Les résultats de recherche retournés sont limités aux articles d’actualité et aux pages découverts par Bing au cours des dernières 24 heures. Le `name` et `URL` pour le premier résultat sont imprimés.

1. Ajoutez ce code à votre projet Python :
    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```
2. Exécutez le programme.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Utiliser la recherche sécurisée, le comptage des réponses et le filtre Promouvoir

Cet exemple utilise les paramètres `answer_count`, `promote` et `safe_search` pour filtrer des résultats de recherche à l’aide de la [`search`méthode](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) du kit de développement logiciel. Le `name` et `URL` pour le premier résultat sont affichés.

1. Ajoutez ce code à votre projet Python :
    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```
2. Exécutez le programme.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous avez terminé ce projet, veillez à supprimer votre clé d’abonnement dans le code du programme et à désactiver votre environnement virtuel.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exemples du SDK Python pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Voir aussi

* [Référence du SDK Python Azure](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
