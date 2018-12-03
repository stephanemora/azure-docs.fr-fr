---
title: 'Démarrage rapide : Kit SDK Recherche visuelle Bing, Python'
titleSuffix: Azure Cognitive Services
description: Programme d’installation de l’application console du Kit de développement logiciel (SDK) de Recherche visuelle Python.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9f2a6d9b75ccf704862d169b96ea1a1f2edb9815
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445682"
---
# <a name="quickstart-bing-visual-search-sdk-python"></a>Démarrage rapide : Kit SDK Recherche visuelle Bing avec Python

Le Kit de développement logiciel (SDK) de Recherche visuelle Bing utilise les fonctionnalités de l’API REST nécessaires pour les requêtes web et l’analyse des résultats.
Le [code source des exemples du Kit de développement logiciel (SDK) de Recherche visuelle Bing Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) est disponible sur GitHub.

Les scénarios de code sont documentés dans les rubriques suivantes :
* [Client Recherche visuelle](#client)
* [Application console complète](#complete-console)
* [Post binaire d’image avec cropArea](#binary-crop)
* [Paramètre KnowledgeRequest](#knowledge-req)
* [Balises, actions et actionType](#tags-actions)

## <a name="application-dependencies"></a>Dépendances de l’application
* Pour ce guide de démarrage rapide, vous devez démarrer un abonnement au niveau tarifaire S9 comme indiqué dans [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Pour démarrer un abonnement dans le portail Azure :
1. Entrez « BingSearchV7 » dans la zone de texte en haut du portail Azure indiquant `Search resources, services, and docs`.  
2. Sous Place de marché dans la liste déroulante, sélectionnez `Bing Search v7`.
3. Entrez `Name` pour la nouvelle ressource.
4. Sélectionnez l’abonnement `Pay-As-You-Go`.
5. Sélectionnez le niveau tarifaire `S9`.
6. Cliquez sur `Enable` pour démarrer l’abonnement.
 
* Si ce n’est déjà fait, installez Python. Le SDK est compatible avec Python 2.7, 3.3, 3.4, 3.5 et 3.6.
* Il est généralement conseillé d’utiliser un [environnement virtuel](https://docs.python.org/3/tutorial/venv.html) pour le développement Python. Installez et initialisez l’environnement virtuel avec le [module venv](https://pypi.python.org/pypi/virtualenv). Installez virtualenv pour Python 2.7.
```
python -m venv mytestenv
```
Installer les dépendances du Kit de développement logiciel (SDK) de Recherche visuelle Bing :
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>Client Recherche visuelle
Pour créer une instance du client `VisualSearchAPI`, importez les bibliothèques suivantes :
```
import http.client, urllib.parse
import json
import os.path
from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,
)
```
Remplacez la valeur de chaîne subscriptionKey par votre clé d’abonnement valide.
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
Ensuite, instanciez le client :
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
Utilisez le client pour rechercher des images et analyser les résultats :
```
PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
    if not result:
        print("No visual search result data.")

        # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")

```

<a name="complete-console"></a> 
## <a name="complete-console-application"></a>Application console complète

L’application console suivante exécute la requête précédemment définie et analyse les résultats :
```
import http.client, urllib.parse
import json
import os.path

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'

PATH = 'C:\\Users\\v-gedod\\azure-cognitive-samples\\mytestenv\\TestImages\\'

from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,)

from msrest.authentication import CognitiveServicesCredentials

client = VisualSearchAPI(CognitiveServicesCredentials(subscription_key))

image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
    if not result:
        print("No visual search result data.")

        # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")


# Uncomment these methods to include code under the following headings of this documentation.
#search_image_binary_with_crop_area(client, subscription_key, PATH)
#search_url_with_filters(client, subscription_key)
#search_insights_token_with_crop_area(client, subscription_key)

```

Les exemples de recherche Bing illustrent différentes fonctionnalités du Kit de développement logiciel (SDK).  Ajoutez les fonctions suivantes à la classe `VisualSrchSDK` précédemment définie.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>Post binaire d’image avec cropArea

Le code suivant envoie un binaire d’image dans le corps de la requête POST, ainsi qu’un objet cropArea.  Il imprime ensuite imageInsightsToken, le nombre de balises, le nombre d’actions et le premier actionType.

```
def search_image_binary_with_crop_area(client, sub_key, file_path):

    #client = VisualSearchAPI(CognitiveServicesCredentials(sub_key))

    image_path = os.path.join(file_path, "image.jpg")
    with open(image_path, "rb") as image_fd:

        crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)
        knowledge_request = VisualSearchRequest(image_info=ImageInfo(crop_area=crop_area))

        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(knowledge_request.serialize())

        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)

        if not result:
            print("No visual search result data.")
            return

        # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")

        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))

            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")


```
<a name="knowledge-req"></a>
## <a name="knowledgerequest-parameter"></a>Paramètre KnowledgeRequest

Le code suivant envoie une URL d’image dans le paramètre `knowledgeRequest`, avec un filtre \"site:pinterest.com\". Il imprime ensuite `imageInsightsToken`, le nombre de balises, le nombre d’actions et le premier actionType.
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="pinterest.com")

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(url=image_url),
        knowledge_request=KnowledgeRequest(filters=filters)
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with url of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

    # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")

```
<a name="tags-actions"></a>
## <a name="tags-actions-and-actiontype"></a>Balises, actions et actionType

Le code suivant envoie un jeton insights d’image dans le paramètre knowledgeRequest, ainsi qu’un objet cropArea. Il imprime ensuite imageInsightsToken, le nombre de balises, le nombre d’actions et le premier actionType.

```
    client = client_in

    image_insights_token = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D"
    crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(
            image_insights_token=image_insights_token,
            crop_area=crop_area
        ),
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with URL of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

    # Visual Search results
    if result.image.image_insights_token:
        print("Uploaded image insights token: {}".format(result.image.image_insights_token))
    else:
        print("Couldn't find image insights token!")

    # List of tags
    if result.tags:
        first_tag = result.tags[0]
        print("Visual search tag count: {}".format(len(result.tags)))

        # List of actions in first tag
        if first_tag.actions:
            first_tag_action = first_tag.actions[0]
            print("First tag action count: {}".format(len(first_tag.actions)))
            print("First tag action type: {}".format(first_tag_action.action_type))
        else:
            print("Couldn't find tag actions!")
    else:
        print("Couldn't find image tags!")
```

## <a name="next-steps"></a>Étapes suivantes

[Exemples du Kit de développement logiciel (SDK) .NET pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).