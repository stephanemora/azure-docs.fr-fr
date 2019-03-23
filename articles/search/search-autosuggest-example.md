---
title: Suggestion automatique d’exemple pour ajouter des termes du contrat de la liste déroulante à une zone de recherche - recherche Azure
description: Ajouter des entrées de requête suggérés en créant des générateurs de suggestions et de formuler des requêtes qui appellent des requêtes suggérées dans Azure Search.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373074"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Exemple : Ajouter suggestion automatique pour les sélections de requête de liste déroulante

Entrées de terme de recherche peuvent inclure une liste déroulante de termes de requête suggérés. Vous avez vu cette fonctionnalité dans les moteurs de recherche commerciale, et vous pouvez implémenter une expérience similaire à l’aide d’Azure Search un [construction du Générateur de suggestions](index-add-suggesters.md) et une opération de suggestions sur une demande de requête. Cet article utilise des exemples pour illustrer la formulation d’une requête de suggestion automatique, à l’aide d’un générateur de suggestions que vous avez déjà défini. 

## <a name="rest-api"></a>API REST

Vous pouvez utiliser [Postman](search-fiddler.md) ou [PowerShell](search-create-index-rest-api.md) et [API REST](https://docs.microsoft.com/rest/api/searchservice/) pour essayer cet exemple, mais les résultats s’affichera en tant que documents JSON. Une expérience plus réaliste et visual est accessible à l’aide de la [exemples de code pour la saisie semi-automatique](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 - l’index avec une construction de générateur de suggestions

Suggestion automatique commence par un [construction du Générateur de suggestions](index-add-suggesters.md) ajoutés à un index, composé de champs qui contribuent de valeurs de liste déroulante. Étant donné le schéma suivant, suggérées seront formuler des requêtes à l’aide de valeurs à partir des champs hotelName et catégorie.

En supposant que les jeux de données exemple minimal trouvé dans les Démarrages rapides, les noms d’hôtel incluent « Rester fantaisie » et « Roach motel » et catégories incluent « Luxury » et « Budget ».

Si vous avez déjà l’index des hôtels, vous devez supprimer et recréer l’en utilisant le schéma suivant. Ce schéma ajoute un générateur de suggestions. Pensez à recharger les données également.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 - interroger avec l’opérateur de suggestions

Pour utiliser un générateur de suggestions et appeler suggestion automatique, vous devez envoyer un [API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) demande à l’aide de GET ou POST. Sur une telle demande, le service de recherche recherchera des correspondances potentielles une fois que les trois premiers caractères sont reçus. 

Dans l’en-tête de demande, définissez **clé api** avec une clé admin ou requête, et **Content-Type** sur application/json. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

Les analyses de demande tous les champs inclus dans le Générateur de suggestions (hotelName et catégorie), retour de la réponse suivante :

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

Pour remettre le résultat attendu, votre code client affichant les résultats en tant que liste déroulante sous une barre de recherche.

## <a name="net-sdk-c"></a>KIT DE DÉVELOPPEMENT LOGICIEL .NET (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 - l’index avec une construction de générateur de suggestions

Dans le SDK .NET, utilisez un [classe de générateur de suggestions](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Générateur de suggestions est une collection, mais elle peut uniquement accepter un seul élément.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 - interroger avec suggérer (méthode)

Le [DocumentsOperationsExtensions.Suggest méthode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) est utilisée pour retourner des chaînes de requête suggérés.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Voir aussi

+ [Explorer les API REST à l’aide de Postman](search-fiddler.md)
+ [Exemple : Autocomplete](search-autocomplete-tutorial.md)
+ [Ajouter des générateurs de suggestions à un index](index-add-suggesters.md)
+ [Ajoutez une classe de générateurs de suggestions à l’aide de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Appeler des suggestions à l’aide de GET ou POST (API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Appeler des suggestions à l’aide de SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) ou 