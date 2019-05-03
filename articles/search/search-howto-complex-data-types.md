---
title: Modélisation de types de données complexes - Recherche Azure
description: Structures de données imbriquées ou hiérarchiques peuvent être modélisées dans un index Azure Search à l’aide des types de données ComplexType et Collections.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 397b3ea7fee67e25cd160f6b529a660e18c44046
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024735"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Modélisation de types de données complexes dans Recherche Azure

Jeux de données externes utilisés pour remplir un index Azure Search parfois inclure sous-structures hiérarchiques ou imbriquées. Exemples peuvent inclure plusieurs emplacements et les numéros de téléphone pour un seul client, plusieurs des couleurs et des tailles pour une référence unique, plusieurs auteurs d’un livre unique et ainsi de suite. En termes de modélisation, vous pouvez voir ces structures appelés *types de données complexes*, *composée des types de données*, *types de données composites*, ou *agrégation types de données*. Dans la terminologie Azure Search, un type complexe est un champ qui contient des enfants (sous-champs) qui à leur tour permettre être simples ou complexes. Cela est similaire à un type de données structurées dans un langage de programmation. Champs complexes peuvent être des champs uniques, qui représentent un objet unique dans le document, ou une collection, qui représente un tableau d’objets

Recherche Azure prend nativement en charge les collections et les types complexes. Ensemble, ces types permettent de modéliser toute structure JSON imbriquée dans un index Azure Search. Dans les versions précédentes d’API de recherche Azure, uniquement aplatie ligne jeux a pu être importés. Dans la version la plus récente, votre index peut mieux correspondre à la source de données. En d’autres termes, si votre source de données possède des types complexes, votre index peut avoir des types complexes aussi.

Pour commencer, nous vous recommandons du [jeu de données d’hôtels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), que vous pouvez charger dans le **importer des données** Assistant dans le portail Azure. L’Assistant détecte les types complexes dans la source et suggère un schéma d’index basé sur les structures détectés.

> [!Note]
> Prise en charge pour les types complexes est la disposition générale dans `api-version=2019-05-06`. 
>
> Si votre solution de recherche est basée sur des solutions de contournement antérieures de jeux de données aplati dans une collection, vous devez modifier votre index pour inclure des types complexes comme pris en charge dans la dernière version d’API. Pour plus d’informations sur la mise à niveau des versions d’API, consultez [mise à niveau vers la dernière version de l’API REST](search-api-migration.md) ou [mise à niveau vers la dernière version du SDK .NET](search-dotnet-sdk-migration.md).

## <a name="example-of-a-complex-structure"></a>Exemple de structure complexe

Le document JSON suivant est composé de champs simples et complexes. Champs complexes, tels que `Address` et `Rooms`, ont des sous-champs. `Address` comprend un seul jeu de valeurs pour ces sous-champs, puisqu’il s’agit d’un objet unique dans le document. En revanche, `Rooms` a plusieurs ensembles de valeurs pour ses sous-champs, un pour chaque objet dans la collection.

```json
{
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
    "Address": {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY"
    },
    "Rooms": [
        {
            "Description": "Budget Room, 1 Queen Bed (Cityside)",
            "Type": "Budget Room",
            "BaseRate": 96.99,
        },
        {
            "Description": "Deluxe Room, 2 Double Beds (City View)",
            "Type": "Deluxe Room",
            "BaseRate": 150.99,
        },
    ]
}
```

## <a name="creating-complex-fields"></a>Création de champs complexes

Comme avec une définition d’index, vous pouvez utiliser le portail, [API REST](https://docs.microsoft.com/rest/api/searchservice/create-index), ou [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) pour créer un schéma qui inclut les types complexes. 

L’exemple suivant montre un schéma d’index JSON avec des champs simples, les collections et les types complexes. Notez que dans un type complexe, chaque champ a un type et peut-être avoir des attributs, des champs simplement comme niveau supérieur. Le schéma correspond à l’exemple de données ci-dessus. `Address` est un champ complex qui n’est pas une collection (un hôtel a une adresse). `Rooms` est un champ de collection complexe (un hôtel a de nombreuses pièces).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with Knowledge Store](knowledge-store-howto.md).
-->

```json
{
    "name": "hotels",
    "fields": [
        {   "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true    },
        {   "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        {   "name": "Address", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        },
        {
            "name": "Rooms",
            "type": "Collection(Edm.ComplexType)",
            "fields": [{
                    "name": "Description",
                    "type": "Edm.String",
                    "searchable": true,
                    "analyzer": "en.lucene"
                },
                {
                    "name": "Type",
                    "type": "Edm.String",
                    "searchable": true
                },
                {
                    "name": "BaseRate",
                    "type": "Edm.Double",
                    "filterable": true,
                    "facetable": true
                },
            ]
        }
    ]
}
```
## <a name="updating-complex-fields"></a>La mise à jour des champs complexes

Tous les [réindexation des règles](search-howto-reindex.md) qui s’appliquent à champs s’appliquent en général la toujours aux champs complexes. Reformulation de quelques-unes des principales règles ici, ajout d’un champ ne nécessite pas une reconstruction d’index, mais effectuer la plupart des modifications.

### <a name="structural-updates-to-the-definition"></a>Mises à jour structurelles à la définition

Vous pouvez ajouter des sous-champs nouveau à un champ complex à tout moment sans avoir besoin d’une reconstruction d’index. Par exemple, ajoutez « ZipCode » à `Address` ou « Équipements » à `Rooms` est autorisé, tout comme l’ajout d’un champ de niveau supérieur à un index. Documents existants ont une valeur null pour les nouveaux champs jusqu'à ce que vous remplissez explicitement ces champs en mettant à jour de vos données.

Notez que dans un type complexe, chaque champ a un type et peut-être avoir des attributs, des champs simplement comme niveau supérieur

### <a name="data-updates"></a>Mises à jour des données

Mise à jour des documents existants dans un index avec l’action de téléchargement fonctionne de la même façon pour les champs complexes et simples : tous les champs sont remplacés. Toutefois, fusion (ou mergeOrUpload lorsqu’il est appliqué à un document existant) ne fonctionne pas les mêmes pour tous les champs. Plus précisément, la fusion n’a pas la possibilité de fusionner des éléments dans une collection. Cela est vrai pour les collections de types primitifs, ainsi que des collections complexes. Pour mettre à jour une collection, vous avez besoin pour récupérer la valeur de la collection complète, apporter des modifications et puis inclure la nouvelle collection dans la demande de l’API d’Index.


## <a name="searching-complex-fields"></a>Recherche dans les champs complexes

Expressions de recherche de forme libre fonctionnent comme prévu avec des types complexes. Si n’importe quel champ de recherche ou le sous-champ de n’importe où dans un document correspond à, le document lui-même est une correspondance. 

Get de requêtes plus nuancée lorsque vous avez plusieurs conditions et des opérateurs, et certains termes ont des noms de champ spécifiés, en l’état possible avec le [syntaxe Lucene](query-lucene-syntax.md). Par exemple, à laquelle cette requête essaie de correspondre à deux termes, « Portland » et « Ou », par rapport à deux sous-champs du champ adresse :

```json
search=Address/City:Portland AND Address/State:OR
```

Ce type de requête est sans corrélation pour la recherche en texte intégral (contrairement aux filtres, où les requêtes des sous-champs d’un ensemble complexe peuvent être mis en corrélation en utilisant l’une ou toutes, comme une sous-requête corrélée dans SQL). Cela signifie que la requête Lucene ci-dessus retourne les documents contenant « Portland, Maine » ainsi que « Portland, Oregon » et d’autres villes dans l’Oregon. Il s’agit, car chaque clause est évalué par rapport à toutes les valeurs du champ spécifié dans l’ensemble du document, donc il n’existe aucune notion d’un sous-élément document « actuel ». 

 

## <a name="selecting-complex-fields"></a>Sélection des champs complexes

Le `$select` paramètre permet de choisir quels champs sont retournés dans les résultats de la recherche. Pour utiliser ce paramètre pour sélectionner des sous-champs spécifiques d’un champ complex, incluez le champ parent et le champ séparés par une barre oblique (`/`).

```json
$select=HotelName, Address/City, Rooms/BaseRate
```

Champs doivent être marqués comme récupérable dans l’index si vous le souhaitez dans les résultats de la recherche. Seuls les champs marqués comme récupérable peuvent être utilisés dans un `$select` instruction. 


## <a name="filter-facet-and-sort-complex-fields"></a>Filtre de facette, champs et tri complexe

Le même [syntaxe de chemin d’accès OData](query-odata-filter-orderby-syntax.md) utilisé pour le filtrage et aucune question pourra traitée recherches peuvent également être utilisées pour les facettes, tri, puis en sélectionnant des champs dans une requête de recherche. Pour les types complexes, les règles s’appliquent qui régissent les sous-champs peuvent être marqués comme pouvant être triés ou à choix multiples. 

### <a name="faceting-sub-fields"></a>Facettes sous-champs 

N’importe quel champ peut être marquée comme « facetable », sauf si elle est de type `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)`. 

Lorsque le nombre de documents est retournées pour la structure de navigation à facettes, les nombres sont par rapport au document parent (un hôtel), pas à des documents imbriqués au sein d’un ensemble complexe (locaux). Par exemple, qu'un hôtel a 20 salles de type « suite ». Étant donné ce paramètre de facette `facet=Rooms/Type`, le nombre de facette sera un pour le document parent (hôtels) et pas les intermédiaires sous-documents (locaux). 

### <a name="sorting-complex-fields"></a>Tri des champs complexes

Opérations de tri s’appliquent aux documents (hôtels) et pas sous-documents (locaux). Lorsque vous avez une collection de type complexe, notamment les salles de, il est important de savoir que vous ne peut pas trier sur les espaces du tout. En fait, vous ne pouvez pas trier sur n’importe quelle collection. 

Opérations de tri fonctionnent quand les champs sont à valeur unique, comme un champ simple, ou comme un champ dans un type complexe. Par exemple, `$orderby=Address/ZipCode` type complexe est triable car il n’existe qu’un seul code postal par hôtel. 

Reformulation de règles autour de tri, au sein d’un champ d’index doivent être marqués comme filtrable et triable, utilisable dans un `$orderby` instruction. 

## <a name="next-steps"></a>Étapes suivantes

 Essayez le [jeu de données d’hôtels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) dans le **importer des données** Assistant. Vous aurez besoin des informations de connexion Cosmos DB fournies dans le fichier Lisezmoi pour accéder aux données. 
 
 Avec ces informations à portée de main, votre première étape dans l’Assistant doit créer une nouvelle source de données Azure Cosmos DB. Davantage sur dans l’Assistant, lorsque vous accédez à la page d’index cible, vous verrez un index avec des types complexes. Créer et charger cet index, puis exécuter des requêtes pour comprendre la nouvelle structure.

> [!div class="nextstepaction"]
> [Démarrage rapide : Assistant du portail pour l’importation, de l’indexation et de requêtes](search-get-started-portal.md)