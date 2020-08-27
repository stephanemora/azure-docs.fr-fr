---
title: Créer un suggesteur
titleSuffix: Azure Cognitive Search
description: Activez les actions de requête type-ahead dans la Recherche cognitive Azure en créant des suggesteurs et en formulant des requêtes qui appellent des termes de requête de suggestion automatique ou d’autocomplétion.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b7918f83d5328c0bf75461d564f3833167c6195e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924552"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Créer un suggesteur pour activer l’autocomplétion et les résultats suggérés dans une requête

Dans la recherche cognitive Azure, la « search-as-you-type » (recherche pendant la saisie) est activée par le biais d’une construction de **suggesteur** ajoutée à un [index de recherche](search-what-is-an-index.md). Un suggesteur prend en charge deux expériences : l’*autocomplétion*, qui complète une entrée partielle pour une recherche sur un terme entier, et les *suggestions*, qui invitent à cliquer sur une correspondance particulière. L’autocomplétion génère une requête. Les suggestions produisent un document correspondant.

Toutes deux sont illustrées par la capture d’écran suivante, extraite de [Créer votre première application en C#](tutorial-csharp-type-ahead-and-suggestions.md). L’autocomplétion anticipe un terme potentiel, en ajoutant « in » si vous avez tapé « tw ». Les suggestions sont des mini-résultats de recherche, où un champ comme Nom de l’hôtel représente un document de recherche d’hôtels correspondants à partir de l’index. Concernant les suggestions, vous pouvez couvrir n’importe quel champ fournissant des informations descriptives.

![Comparaison visuelle des requêtes de saisie semi-automatique et suggérées](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Comparaison visuelle des requêtes de saisie semi-automatique et suggérées")

Vous pouvez utiliser ces fonctionnalités séparément ou ensemble. Pour implémenter ces comportements dans la Recherche cognitive Azure, il existe un composant de requête et d’index. 

+ Dans l’index, ajoutez un suggesteur à un index. Vous pouvez utiliser le portail, l'[API REST](/rest/api/searchservice/create-index) ou le [kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). La suite de cet article se concentre sur la création d’un suggesteur.

+ Dans la requête, appelez une des [API répertoriées ci-dessous](#how-to-use-a-suggester).

La prise en charge de l’expérience « search-as-you-type » est activée par champ pour des champs de chaînes. Vous pouvez implémenter les deux comportements prédictifs dans la même solution de recherche si vous souhaitez une expérience similaire à celle indiquée dans la capture d’écran. Les deux requêtes ciblent la collection de *documents* d’un index spécifique, et les réponses sont renvoyées après qu’un utilisateur a saisi une chaîne d’entrée avec trois caractères minimum.

## <a name="what-is-a-suggester"></a>Qu’est-ce qu’un suggesteur ?

Un suggesteur est une structure de données interne prenant en charge des comportements « search-as-you-type » en stockant des préfixes en vue d’une correspondance lors de requêtes partielles. Comme avec les termes tokenisés, les préfixes sont stockés dans des index inversés, soit un pour chaque champ spécifié dans une collection de champs de suggesteurs.

## <a name="define-a-suggester"></a>Définir un suggesteur

Pour créer un suggesteur, ajoutez-en un à un [schéma d’index](/rest/api/searchservice/create-index) et [définissez chaque propriété](#property-reference). Le meilleur moment pour créer un suggesteur est lorsque vous définissez également le champ qui va l’utiliser.

+ Utiliser uniquement des champs de chaîne

+ Utilisez l’analyseur Lucene standard par défaut (`"analyzer": null`) ou un [analyseur de langage](index-add-language-analyzers.md) (par exemple, `"analyzer": "en.Microsoft"`) sur le champ.

### <a name="choose-fields"></a>Choisir des champs

Bien qu’un suggesteur possède plusieurs propriétés, il se compose principalement d’une collection de champs de chaîne pour lesquels vous activez une expérience « search-as-you-type ». Il existe un suggesteur pour chaque index ; la liste des suggesteurs doit donc inclure tous les champs qui contribuent au contenu des suggestions et de l’autocomplétion.

L’autocomplétion bénéficie d’un plus grand nombre de champs à exploiter, car le contenu supplémentaire a un potentiel d’achèvement des termes plus important.

En revanche, les suggestions produisent de meilleurs résultats lorsque votre choix de champ est sélectif. N’oubliez pas que la suggestion est un proxy pour un document de recherche, vous voudrez donc les champs qui représentent le mieux un résultat unique. Les noms, titres ou autres champs uniques qui font la distinction entre plusieurs correspondances fonctionnent le mieux. Si les champs sont constitués de valeurs répétitives, les suggestions consistent en des résultats identiques, et l’utilisateur ne saura pas sur lequel cliquer.

Pour convenir aux deux expériences « search-as-you-type », ajoutez tous les champs dont vous avez besoin pour l’autocomplétion, mais utilisez **$select**, **$top**, **$filter** et **searchFields** pour contrôler les résultats des suggestions.

### <a name="choose-analyzers"></a>Choisir des analyseurs

L’analyseur choisi détermine la façon dont les champs sont tokenisés et préfixés par la suite. Par exemple, pour une chaîne avec trait d’union comme « context-sensitve », l’utilisation d’un analyseur de langage entraînera les combinaisons de jetons suivantes : « context », « sensitive », « context-sensitive ». Si vous aviez utilisé l’analyseur Lucene standard, la chaîne avec trait d’union n’existerait pas. 

Lors de l’évaluation des analyseurs, envisagez d’utiliser l’[API d’analyse de texte](/rest/api/searchservice/test-analyzer) pour savoir comment les termes sont tokenisés et préfixés par la suite. Une fois que vous avez créé un index, vous pouvez essayer divers analyseurs sur une chaîne pour afficher la sortie du jeton.

Les champs qui utilisent des [analyseurs personnalisés](index-add-custom-analyzers.md) ou des [analyseurs prédéfinis](index-add-custom-analyzers.md#predefined-analyzers-reference) (à l’exception de Lucene standard) sont explicitement interdits pour éviter des résultats médiocres.

> [!NOTE]
> Si vous avez besoin de contourner la contrainte de l’analyseur, par exemple, si vous avez besoin d’un analyseur de mot clé ou de ngram pour certains scénarios de requête, vous devez utiliser deux champs distincts pour le même contenu. Ceci permettra à l’un des champs d’avoir un suggesteur et à l'autre d'être configuré avec une configuration d’analyseur personnalisée.

### <a name="when-to-create-a-suggester"></a>Quand créer un suggesteur

La création de la définition de champs est le moment idéal pour créer un suggesteur.

L'API ne vous autorise pas à créer un suggesteur à l’aide de champs préexistants. Des préfixes sont générés pendant l’indexation, lorsque des termes partiels combinant deux caractères ou plus sont tokenisés avec des termes entiers. Les champs existants étant déjà tokenisés, vous devez recréer l'index pour les ajouter à un suggesteur. Pour plus d’informations, consultez [Comment regénérer un index de recherche cognitive Azure](search-howto-reindex.md).

## <a name="create-using-rest"></a>Créer à l’aide de REST

Dans l’API REST, ajoutez des suggesteurs via [Créer un index](/rest/api/searchservice/create-index) ou [Mettre à jour l’index](/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Créer à l’aide de .NET

Dans C#, définissez un [objet de suggesteur](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` est une collection, mais elle peut uniquement accepter un élément. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Informations de référence sur les propriétés

|Propriété      |Description      |
|--------------|-----------------|
|`name`        |Nom du suggesteur.|
|`searchMode`  |Stratégie utilisée pour rechercher des expressions candidates. Le seul mode actuellement pris en charge est `analyzingInfixMatching`, qui établit actuellement une correspondance au début d’un terme.|
|`sourceFields`|Liste d’un ou de plusieurs champs constituant la source du contenu pour des suggestions. Les champs doivent être de type `Edm.String` et `Collection(Edm.String)`. Tout analyseur spécifié dans le champ doit correspondre à un analyseur nommé issu de [cette liste](/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (et non à un analyseur personnalisé).<p/> Il vous est conseillé de spécifier uniquement les champs qui se prêtent à une réponse attendue et appropriée, qu’il s’agisse d’une chaîne complète dans une barre de recherche ou d’une liste déroulante.<p/>Un nom d’hôtel est un bon candidat, car il est précis. Les champs détaillés tels que des descriptions et des commentaires sont trop denses. De même, les champs répétitifs, tels que les balises et les catégories sont moins efficaces. Dans les exemples, nous incluons tout de même « catégorie » pour montrer que vous pouvez inclure plusieurs champs. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Utiliser un suggesteur

Un suggesteur est utilisé dans une requête. Après la création d’un suggesteur, appelez l’une des API suivantes pour tester une expérience « search-as-you-type » :

+ [REST API Suggestions](/rest/api/searchservice/suggestions) 
+ [API REST Autocomplétion](/rest/api/searchservice/autocomplete) 
+ [Méthode SuggestWithHttpMessagesAsync](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Méthode AutocompleteWithHttpMessagesAsync](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Dans une application de recherche, le code client doit utiliser une bibliothèque comme [jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) pour collecter la requête partielle et fournir la correspondance. Pour plus d’informations sur cette tâche, consultez [Ajouter l’autocomplétion ou les résultats suggérés au code client](search-autocomplete-tutorial.md).

L’utilisation de l’API est illustrée dans l’appel suivant de l’API REST Autocomplétion. Dans cet exemple, deux éléments importants sont à retenir. Tout d’abord, comme avec toutes les requêtes, l’opération est effectuée sur la collection de documents d’un index et la requête inclut un paramètre de **recherche**, qui, dans ce cas, fournit la requête partielle. Ensuite, vous devez ajouter **suggesterName** à la requête. Si un suggesteur n’est pas défini dans l’index, tout appel à l'autocomplétion ou aux suggestions se soldera par un échec.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Exemple de code

+ L’exemple [Créer votre première application en C# (Leçon 3 : Ajouter « search-as-you-type »)](tutorial-csharp-type-ahead-and-suggestions.md) illustre une construction de suggesteur, des requêtes suggérées, l’autocomplétion et la navigation par facettes. Cet exemple de code s’exécute dans un service Recherche cognitive Azure de bac à sable et utilise un index des hôtels préchargé. Vous n’avez plus qu’à appuyer sur F5 pour exécuter l’application. Aucun abonnement ou connexion n’est nécessaire.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) correspond à un précédent exemple contenant du code C# et Java. Il illustre une construction de suggesteur, des requêtes suggérées, l'autocomplétion et la navigation par facettes. Cet exemple de code utilise les exemples de données hébergées [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). 

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons l’article suivant pour en savoir plus sur la formulation des requêtes.

> [!div class="nextstepaction"]
> [Ajouter l’autocomplétion et les suggestions au code client](search-autocomplete-tutorial.md)