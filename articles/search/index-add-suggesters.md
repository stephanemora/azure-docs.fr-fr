---
title: Ajouter des requêtes type-ahead à un index
titleSuffix: Azure Cognitive Search
description: Activez les actions de requête type-ahead dans la Recherche cognitive Azure en créant des suggesteurs et en formulant des requêtes qui appellent des termes de requête de suggestion automatique ou d’autocomplétion.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790102"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Ajouter des suggesteurs à un index pour des requêtes type-ahead dans la Recherche cognitive Azure

Dans la Recherche cognitive Azure, la fonctionnalité « search-as-you-type » (recherche au fur et à mesure de la frappe) ou type-ahead repose sur une structure de **suggesteur** que vous ajoutez à un [index de recherche](search-what-is-an-index.md). Il s’agit d’une liste à un ou plusieurs champs où vous souhaitez activer la requête prédictive.

Un suggesteur prend en charge deux variantes prédictives : l'*autocomplétion* qui complète le terme ou l’expression que vous entrez, et les *suggestions* qui renvoient une courte liste de documents correspondants.  

La capture d’écran suivante, extraite de l'exemple [Créer votre première application en C#](tutorial-csharp-type-ahead-and-suggestions.md), illustre les requêtes prédictives. L'autocomplétion anticipe ce que l’utilisateur peut entrer dans la zone de recherche. L’entrée indique « tw », dont l'autocomplétion se termine par « in » pour donner « twin » en tant que terme de recherche potentiel. Les suggestions s'affichent dans la liste déroulante. Pour les suggestions, vous pouvez faire apparaître n’importe quelle partie d’un document qui décrit le mieux le résultat. Dans cet exemple, les suggestions correspondent à des noms d’hôtels. 

![Comparaison visuelle des requêtes de saisie semi-automatique et suggérées](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparaison visuelle des requêtes de saisie semi-automatique et suggérées")

Pour implémenter ces comportements dans la Recherche cognitive Azure, il existe un composant de requête et d’index. 

+ Dans l’index, ajoutez un suggesteur à un index. Vous pouvez utiliser le portail, l'[API REST](https://docs.microsoft.com/rest/api/searchservice/create-index) ou le [kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). La suite de cet article se concentre sur la création d’un suggesteur. 

+ Dans la requête, appelez une des [API répertoriées ci-dessous](#how-to-use-a-suggester).

La prise en charge de l’expérience « search-as-you-type » est activée par champ. Vous pouvez implémenter les deux comportements prédictifs dans la même solution de recherche si vous souhaitez une expérience similaire à celle indiquée dans la capture d’écran. Les deux requêtes ciblent la collection de *documents* d’un index spécifique, et les réponses sont renvoyées après qu’un utilisateur a saisi une chaîne d’entrée avec trois caractères minimum.

## <a name="create-a-suggester"></a>Créer un suggesteur

Bien qu’un suggesteur possède plusieurs propriétés, il se compose principalement d’une collection de champs pour lesquels vous activez une expérience prédictive. Par exemple, une application de voyage peut souhaiter activer la recherche prédictive sur des destinations, villes et attractions. Dans un tel scénario, les trois champs rejoindraient la collection de champs.

Pour créer un suggesteur, ajoutez-en un à un schéma d’index. Vous pouvez disposer d’un suggesteur dans un index (et plus particulièrement, un suggesteur dans la collection de suggesteurs). 

### <a name="when-to-create-a-suggester"></a>Quand créer un suggesteur

La création de la définition de champs est le moment idéal pour créer un suggesteur.

L'API ne vous autorise pas à créer un suggesteur à l’aide de champs préexistants. Le texte prédictif est créé pendant l’indexation, lorsque des termes partiels combinant deux caractères ou plus sont tokenisés avec des termes entiers. Les champs existants étant déjà tokenisés, vous devez recréer l'index pour les ajouter à un suggesteur. Pour plus d’informations sur la réindexation, consultez [Guide pratique pour regénérer un index Recherche cognitive Azure](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>À l’aide de l’API REST

Dans l’API REST, ajoutez des suggesteurs via [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>À l’aide du kit de développement logiciel (SDK) .NET

Dans C#, définissez un [objet de suggesteur](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` est une collection, mais elle peut uniquement accepter un élément. 

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

### <a name="property-reference"></a>Informations de référence sur les propriétés

|Propriété      |Description      |
|--------------|-----------------|
|`name`        |Nom du suggesteur.|
|`searchMode`  |Stratégie utilisée pour rechercher des expressions candidates. Le seul mode actuellement pris en charge est `analyzingInfixMatching`, qui effectue une correspondance flexible des expressions en début ou au milieu des phrases.|
|`sourceFields`|Liste d’un ou de plusieurs champs constituant la source du contenu pour des suggestions. Les champs doivent être de type `Edm.String` et `Collection(Edm.String)`. Tout analyseur spécifié dans le champ doit correspondre à un analyseur nommé issu de [cette liste](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (et non à un analyseur personnalisé).<p/>Il vous est conseillé de spécifier uniquement les champs qui se prêtent à une réponse attendue et appropriée, qu’il s’agisse d’une chaîne complète dans une barre de recherche ou d’une liste déroulante.<p/>Un nom d’hôtel est un bon candidat, car il est précis. Les champs détaillés tels que des descriptions et des commentaires sont trop denses. De même, les champs répétitifs, tels que les balises et les catégories sont moins efficaces. Dans les exemples, nous incluons tout de même « catégorie » pour montrer que vous pouvez inclure plusieurs champs. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Restrictions de l’analyseur pour sourceFields dans un suggesteur

La Recherche cognitive Azure analyse le contenu des champs pour permettre l’interrogation des termes individuels. Les générateurs de suggestions requièrent l’indexation des préfixes en plus des termes complets, ce qui nécessite une analyse supplémentaire sur les champs sources. Les configurations de l’analyseur personnalisé peuvent combiner les différents générateurs de jetons et filtres, souvent de manière à produire les préfixes requis pour des suggestions impossibles. Dès lors, la Recherche cognitive Azure empêche les champs dotés d’analyseurs personnalisés d’être inclus dans un suggesteur.

> [!NOTE] 
>  S'il vous faut contourner la limitation ci-dessus, utilisez deux champs distincts pour le même contenu. Ceci permettra à l’un des champs d’avoir un suggesteur et à l'autre d'être configuré avec une configuration d’analyseur personnalisée.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Utiliser un suggesteur dans une requête

Après avoir créé un suggesteur, appelez l'API qui convient dans votre logique de requête pour appeler la fonctionnalité. 

+ [REST API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API REST Autocomplétion](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Méthode SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Méthode AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

L’utilisation de l’API est illustrée dans l’appel suivant de l’API REST Autocomplétion. Dans cet exemple, deux éléments importants sont à retenir. Premièrement, comme pour toutes les requêtes, l’opération porte sur la collection de documents d’un index. Deuxièmement, vous pouvez ajouter des paramètres de requête. Si de nombreux paramètres de requête sont communs aux deux API, la liste diffère pour chacune d’entre elles.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Si un suggesteur n’est pas défini dans l’index, tout appel à l'autocomplétion ou aux suggestions se soldera par un échec.

## <a name="sample-code"></a>Exemple de code

+ L'exemple [Créer votre première application en C#](tutorial-csharp-type-ahead-and-suggestions.md) illustre une construction de suggesteur, des requêtes suggérées, l'autocomplétion et la navigation par facettes. Cet exemple de code s’exécute dans un service Recherche cognitive Azure de bac à sable et utilise un index des hôtels préchargé. Vous n’avez plus qu’à appuyer sur F5 pour exécuter l’application. Aucun abonnement ou connexion n’est nécessaire.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) correspond à un précédent exemple contenant du code C# et Java. Il illustre une construction de suggesteur, des requêtes suggérées, l'autocomplétion et la navigation par facettes. Cet exemple de code utilise les exemples de données hébergées [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). 


## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons l’exemple suivant pour découvrir la formulation des demandes.

> [!div class="nextstepaction"]
> [Exemples de suggestions et d’autocomplétion](search-autocomplete-tutorial.md) 
