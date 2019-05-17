---
title: 'Démarrage rapide : Python et l’API REST - recherche Azure'
description: Créer, charger et interroger un index à l’aide de Python, Jupyter Notebooks et l’API REST Azure Search.
ms.date: 05/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 1ab6bb069f60f4d2dbb4cfaecda54c3c2ef20adc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806430"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Démarrage rapide : Créer un index Azure Search à l’aide du bloc-notes Jupyter Python
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Créer un bloc-notes Jupyter qui crée, charge et interroge une recherche Azure [index](search-what-is-an-index.md) à l’aide de Python et le [API REST de Service de recherche Azure](https://docs.microsoft.com/rest/api/searchservice/). Cet article explique comment créer votre propre portable étape par étape. Si vous le souhaitez, vous pouvez exécuter un bloc-notes terminé. Pour télécharger une copie, accédez à [référentiel Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples).

Si vous ne disposez d’aucun abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer, puis [inscrivez-vous auprès de la fonction Recherche Azure](search-create-service-portal.md).

## <a name="prerequisites"></a>Conditions préalables

Les services et les outils qui suivent sont utilisés dans ce guide de démarrage rapide. 

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), fournissant les Python 3.x et Notebooks Jupyter.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir une clé d’accès et un point de terminaison HTTP](media/search-fiddler/get-url-key.png "Obtenir une clé d’accès et un point de terminaison HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="connect-to-azure-search"></a>Se connecter à la Recherche Azure

Ouvrez un bloc-notes Jupyter et vérifier la connexion à partir de votre station de travail locale en demandant une liste des index sur votre service. Sur Windows avec Anaconda3, vous pouvez utiliser Anaconda Navigator pour lancer un bloc-notes.

1. Créer un nouveau bloc-notes Python3.

1. Dans la première cellule, chargez les bibliothèques utilisées pour travailler avec JSON et formuler des requêtes HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Dans la deuxième cellule, entrez les éléments de la demande qui seront des constantes à chaque demande. Remplacez le nom de service de recherche (YOUR-SEARCH-SERVICE-NAME) et la clé d’API d’administration (YOUR-ADMIN-API-KEY) avec des valeurs valides. 

   ```python
    endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
    api_version = '?api-version=2019-05-06'
    headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Dans la troisième cellule, formuler la demande. Cette demande GET cible la collection d’index de votre service de recherche et sélectionne la propriété de nom.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Exécuter chaque étape. Si les index existent, la réponse contient une liste d’index. Dans la capture d’écran ci-dessous, le service inclut un azureblob-l’index et un index realestate-us-sample.

   ![Le script Python dans Jupyter notebook avec HTTP demande à Azure Search](media/search-get-started-python/connect-azure-search.png "script Python dans Jupyter notebook avec HTTP demande à Azure Search")

   Une collection d’index vide retourne cette réponse : `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

> [!Tip]
> Sur un service gratuit, vous êtes limité à trois index, indexeurs et sources de données. Ce démarrage rapide crée une occurrence de chacun. Vérifiez que vous disposez de la place pour créer de nouveaux objets avant d’aller plus loin.

## <a name="1---create-an-index"></a>1 – Créer un index

Sauf si vous utilisez le portail, un index doit exister sur le service avant de pouvoir charger des données. Cette étape utilise le [API REST création d’Index](https://docs.microsoft.com/rest/api/searchservice/create-index) pour pousser un schéma d’index vers le service

La collection fields définit la structure d’un *document*. Les éléments requis d’un index incluent un nom et une collection de champs. Chaque champ a un nom, type et les attributs qui déterminent la façon dont il est utilisé (par exemple, si elle est en texte intégral consultable, filtrable ou récupérables dans les résultats de la recherche). Dans un index, l’un des champs de type `Edm.String` doit être désigné comme le *clé* pour l’identité du document.

Cet index est nommé « hotels-py » et a les définitions de champ que vous voyez ci-dessous. C’est un sous-ensemble d’une plus grande [index des hôtels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) utilisé dans les autres procédures pas à pas. Nous avons le tronqué dans ce démarrage rapide par souci de concision.


1. Dans la cellule suivante, collez l’exemple suivant dans une cellule pour fournir le schéma. 

    ```python
    index_schema = {
       "name": "hotels-py",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Dans une autre cellule, formuler la demande. Cette PUT demande cible de la collection d’index de votre service de recherche et crée un index basé sur le schéma d’index que vous avez fourni à l’étape précédente.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Exécuter chaque étape.

   La réponse inclut à la représentation JSON du schéma. La capture d’écran suivante supprime les parties du schéma d’index pour que vous puissiez voir plus de la réponse.

    ![Demande de création d’un index](media/search-get-started-python/create-index.png "demande de création d’un index")

> [!Tip]
> Pour la vérification, vous pourrez également vérifier la liste des index dans le portail, ou réexécutez la demande de connexion de service pour voir les *hôtels-py* index répertoriés dans la collection d’index.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – Charger des documents

Pour transmettre des documents, utilisez une requête HTTP POST au point de terminaison URL de votre index. L’API REST est [Ajout, mise à jour ou supprimer des Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Documents proviennent de [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) sur GitHub.

1. Dans une nouvelle cellule, fournissent trois documents conformes au schéma d’index. Spécifiez une action de téléchargement pour chaque document.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        }
      }
     ]
    }
    ```

2. Dans une autre cellule, formuler la demande. Cette requête POST cible la collection docs de l’index des hôtels-py et envoie les documents fournis à l’étape précédente.

   ```python
   url = endpoint + "indexes/hotels-py/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Exécuter chaque étape pour envoyer les documents à un index dans votre service de recherche. Résultats doivent ressembler à l’exemple suivant. 

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
    'value': [{'errorMessage': None,
            'key': '1',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '2',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '3',
            'status': True,
            'statusCode': 201}]}
     ```


## <a name="3---search-an-index"></a>3 – Rechercher dans un index

Cette étape vous montre comment interroger un index à l’aide de la [API REST de recherche Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents).


1. Dans une nouvelle cellule, fournissez une expression de requête. L’exemple suivant effectue une recherche sur les termes « hôtels » et « wifi ». Il retourne également un *nombre* de documents qui correspondent, et *sélectionne* les champs à inclure dans les résultats de recherche.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

2. Formuler une requête. Cette demande GET cible la collection docs de l’index des hôtels-py et attache la requête que vous avez spécifié à l’étape précédente.

   ```python
   url = endpoint + "indexes/hotels-py/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

   Résultats doivent ressembler à la sortie suivante.

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#docs(*)",
    '@odata.count': 3,
    'value': [{'@search.score': 1.0,
               'HotelId': '1',
               'HotelName': 'Secret Point Motel'},
              {'@search.score': 1.0,
               'HotelId': '2',
               'HotelName': 'Twin Dome Motel'},
              {'@search.score': 1.0,
               'HotelId': '3',
               'HotelName': 'Triple Landscape Hotel'}]}
   ```

3. Essayez quelques autres exemples de requête pour obtenir un aperçu de la syntaxe. Vous pouvez appliquer un filtre, prendre les deux premiers résultats, classer par un champ spécifique, ou 

   + `searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=hotel&$top=2&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'`

## <a name="clean-up"></a>Nettoyer 

Vous devez supprimer l’index si vous n’avez plus besoin. Un service gratuit est limité à trois index. Vous souhaiterez peut-être supprimer n’importe quel index que vous n’utilisez pas activement pour faire place à d’autres didacticiels.

   ```python
  url = endpoint + "indexes/hotels-py" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Vous pouvez vérifier la suppression d’index en renvoyant une liste des index existants. Si hôtels-py a disparu, vous savez votre demande a réussi.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les scénarios et syntaxe de requête.

> [!div class="nextstepaction"]
> [Créer une requête de base](search-query-overview.md)
