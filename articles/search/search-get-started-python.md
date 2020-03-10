---
title: 'Démarrage rapide : Créer un index de recherche en Python à l’aide des API REST'
titleSuffix: Azure Cognitive Search
description: Explique comment créer un index, charger des données et exécuter des requêtes en Python à l’aide de notebooks Jupyter et de l’API REST de la Recherche cognitive Azure.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 93fb9ec735de1abf89eb217d0f4096fcfc0afe94
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227098"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Démarrage rapide : Créer un index Recherche cognitive Azure en Python à l’aide de notebooks Jupyter

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Portail](search-create-index-portal.md)
> 

Générez un notebook Jupyter qui crée, charge et interroge un index Recherche cognitive Azure en Python à l’aide des [API REST de la Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/). Cet article décrit la procédure à suivre pour créer un notebook. Vous pouvez [également télécharger et exécuter un notebook Jupyter Python complet](https://github.com/Azure-Samples/azure-search-python-samples).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Les services et outils suivants sont indispensables dans ce guide de démarrage rapide. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), qui fournit des notebooks Python 3.x et Jupyter.

+ [Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser le niveau gratuit pour ce démarrage rapide. 

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir un point de terminaison et une clé d’accès HTTP](media/search-get-started-postman/get-url-key.png "Obtenir un point de terminaison et une clé d’accès HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="connect-to-azure-cognitive-search"></a>Se connecter à la Recherche cognitive Azure

Dans cette tâche, démarrez un notebook Jupyter, puis vérifiez que vous pouvez vous connecter à la Recherche cognitive Azure. Pour ce faire, demandez une liste d’index de votre service. Sur Windows avec Anaconda3, vous pouvez utiliser Anaconda Navigator pour lancer un notebook.

1. Créez un notebook Python3.

1. Dans la première cellule, chargez les bibliothèques utilisées pour travailler avec JSON et formuler des requêtes HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Dans la deuxième cellule, entrez les éléments de requête qui seront des constantes sur chaque requête. Remplacez le nom du service de recherche (YOUR-SEARCH-SERVICE-NAME) et la clé API d’administration (YOUR-ADMIN-API-KEY) par des valeurs valides. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Si vous recevez ConnectionError `"Failed to establish a new connection"`, vérifiez que la clé API est une clé d’administration principale ou secondaire, et que tous les caractères de début et de fin (`?` et `/`) sont en place.

1. Dans la troisième cellule, formulez la requête. Cette requête GET cible la collection d’index de votre service de recherche et sélectionne la propriété de nom des index existants.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Exécutez chaque étape. Si des index existent, la réponse contient la liste des noms d’index. Dans la capture d’écran ci-dessous, le service a déjà un index azureblob et un index realestate-us-sample.

   ![Script Python dans le notebook Jupyter avec des requêtes HTTP pour la Recherche cognitive Azure](media/search-get-started-python/connect-azure-search.png "Script Python dans le notebook Jupyter avec des requêtes HTTP pour la Recherche cognitive Azure")

   En revanche, une collection d’index vide retourne cette réponse : `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 – Créer un index

Sauf si vous utilisez le portail, le service doit contenir un index pour vous permettre de charger des données. Cette étape utilise l’[API REST Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index) pour envoyer un schéma d’index au service.

Les éléments requis d’un index sont un nom, une collection de champs et une clé. La collection de champs définit la structure d'un *document*. Chaque champ a un nom, un type et des attributs qui déterminent la façon dont il est utilisé (par exemple, s’il permet d’effectuer une recherche en texte intégral, et s’il est filtrable ou récupérable dans des résultats de recherche). Dans un index, l’un des champs de type `Edm.String` doit être désigné comme *clé* pour l’identité du document.

Cet index est nommé « hotels-quickstart » et contient les définitions de champ que vous voyez ci-dessous. Il s’agit d’un sous-ensemble d’un [index Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) plus grand utilisé dans d’autres procédures pas à pas. Nous l’avons volontairement tronqué dans ce démarrage rapide par souci de concision.

1. Dans la cellule suivante, collez l’exemple suivant dans une cellule pour fournir le schéma. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
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

2. Dans une autre cellule, formulez la requête. Cette requête POST cible la collection d’index de votre service de recherche et crée un index basé sur le schéma d’index que vous avez fourni dans la cellule précédente.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Exécutez chaque étape.

   La réponse inclut la représentation JSON du schéma. La capture d’écran suivante présente uniquement une partie de la réponse.

    ![Requête de création d’un index](media/search-get-started-python/create-index.png "Requête de création d’un index")

> [!Tip]
> Une autre manière de vérifier la création d’index consiste à consulter la liste Index dans le portail.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – Charger des documents

Pour envoyer (push) des documents, utilisez une requête HTTP POST au point de terminaison de l’URL de votre index. L’API REST est destinée à l’[ajout, la mise à jour ou la suppression de documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Les documents proviennent de [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) sur GitHub.

1. Dans une nouvelle cellule, fournissez quatre documents conformes au schéma d’index. Spécifiez une action de chargement pour chaque document.

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
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. Dans une autre cellule, formulez la requête. Cette requête POST cible la collection de documents de l’index de démarrage rapide hotels, et envoie les documents fournis à l’étape précédente.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Exécutez chaque étape pour envoyer les documents à un index dans votre service de recherche. Les résultats doivent ressembler à l’exemple suivant. 

    ![Envoyer des documents à un index](media/search-get-started-python/load-index.png "Envoyer des documents à un index")

## <a name="3---search-an-index"></a>3 – Rechercher dans un index

Cette étape vous montre comment interroger un index à l’aide de l’[API REST Rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Dans une cellule, fournir une expression de requête qui exécute une recherche vide (recherche=*), retournant une liste non classée (résultat de la recherche=1.0) de documents arbitraires. Par défaut, la Recherche cognitive Azure retourne 50 correspondances à la fois. Telle qu’elle est structurée, cette requête renvoie la structure et les valeurs d’un document entier. Ajoutez $count=true pour obtenir le nombre de tous les documents dans les résultats.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Dans une nouvelle cellule, fournissez l’exemple suivant pour rechercher les termes « hotels » et « wifi ». Ajoutez $select pour spécifier les champs à inclure dans les résultats de recherche.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Dans une autre cellule, formulez une requête. Cette requête GET cible la collection de documents de l’index de démarrage rapide hotels, et attache la requête que vous avez spécifié à l’étape précédente.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Exécutez chaque étape. Les résultats se présentent comme suit. 

    ![Rechercher dans un index](media/search-get-started-python/search-index.png "Rechercher dans un index")

1. Essayez quelques autres exemples de requête pour avoir un aperçu de la syntaxe. Vous pouvez remplacer la `searchstring` par les exemples suivants, puis réexécuter la requête de recherche. 

   Appliquez un filtre : 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Prenez les deux premiers résultats :

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Triez sur un champ spécifique :

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Nettoyer

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

## <a name="next-steps"></a>Étapes suivantes

Par souci de simplification, ce démarrage rapide utilise une version abrégée de l’index Hotels. Vous pouvez créer la version complète pour essayer des requêtes plus intéressantes. Pour obtenir la version complète et les 50 documents, exécutez l’Assistant **Importation de données** en sélectionnant *hotels-sample* à partir des exemples de sources de données intégrés.

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un index dans le portail Azure](search-get-started-portal.md)
