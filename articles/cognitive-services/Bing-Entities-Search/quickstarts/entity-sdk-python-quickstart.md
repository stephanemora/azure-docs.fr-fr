---
title: 'Démarrage rapide : SDK Recherche d’entités Bing, Python'
titlesuffix: Azure Cognitive Services
description: Configuration de l’application console du Kit de développement logiciel (SDK) pour Recherche d’entités Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9a66df03ceb03885ba5f61b4adeb54cb1876338d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813609"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Démarrage rapide : SDK Recherche d’entités Bing avec Python

Utilisez ce guide de démarrage rapide pour commencer à rechercher des entités avec le SDK Recherche d’entités Bing pour Python. Si l’outil Recherche d’entités Bing a une API REST compatible avec la plupart des langages de programmation, le SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Prérequis

* Python[ 2.x ou 3.x](https://www.python.org/)

* [SDK Recherche d’entités Bing pour Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Nous vous recommandons d’utiliser un environnement virtuel python. Vous pouvez installer et lancer un environnement virtuel avec le module venv. Vous pouvez installer virtualenv pour Python 2.7 avec :

```Console
python -m venv mytestenv
```

Installez le SDK Recherche d’entités Bing avec :

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis ajoutez les instructions d’importation suivantes. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Créez une variable pour votre clé d’abonnement et instanciez le client en créant un objet `CognitiveServicesCredentials` avec cette variable.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Envoyer une requête de recherche et recevoir une réponse

1. Envoyez une requête de recherche à Recherche d’entités Bing avec `client.entities.search()` et ladite requête. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Si des entités sont retournées, convertissez `entity_data.entities.value` en liste et imprimez le premier résultat.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorial-bing-entities-search-single-page-app.md)

* [Qu’est-ce que l’API Recherche d’entités Bing ?](../overview.md )