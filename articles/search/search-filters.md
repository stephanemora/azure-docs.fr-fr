---
title: Filtrer les résultats de la recherche
titleSuffix: Azure Cognitive Search
description: Filtrez par identité de sécurité d’utilisateur, langue, emplacement géographique ou valeurs numériques pour réduire les résultats de recherche des requêtes dans la Recherche cognitive Azure, un service de recherche dans le cloud hébergé sur Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a5c8f835d44896a452a945614332dcbc25ca8bb8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694425"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtres dans la Recherche cognitive Azure 

Un *filtre* établit des critères basés sur des valeurs pour sélectionner les documents utilisés dans une requête. Un filtre peut être une valeur unique ou une [expression de filtre](search-query-odata-filter.md) OData. Contrairement à la recherche en texte intégral, une valeur ou une expression de filtre ne renvoie qu’une correspondance stricte.

Certaines expériences de recherche, telles que la [navigation par facettes](search-filters-facets.md), dépendent des filtres dans le cadre de l’implémentation. Toutefois, vous pouvez utiliser des filtres chaque fois que vous souhaitez restreindre une requête à des valeurs spécifiques. Si vous souhaitez plutôt restreindre une requête à des champs spécifiques, il existe d’autres méthodes que nous vous présentons ci-dessous.

## <a name="when-to-use-a-filter"></a>Quand utiliser un filtre

Certains filtres sont fondamentaux pour diverses expériences de recherche. C’est notamment le cas des filtres de « recherche à proximité », de navigation par facettes et de sécurité, qui montrent uniquement les documents qu’un utilisateur est autorisé à afficher. Si vous implémentez l’une de ces expériences, un filtre est requis. C’est le filtre associé à la requête de recherche qui fournit les coordonnées de géolocalisation, la catégorie de facettes sélectionnée par l’utilisateur, ou l’ID de sécurité du demandeur.

Voici les scénarios les plus courants :

+ Répartition des résultats de la recherche en fonction du contenu de l’index. À partir d’un schéma indiquant l’emplacement, les catégories et les équipements de l’hôtel, vous pouvez créer un filtre qui correspond explicitement aux critères (à Seattle, sur l’eau, avec une vue). 

+ Implémentation d’une expérience de recherche avec une condition de filtre :

  + La [navigation par facettes](search-faceted-navigation.md) utilise un filtre pour re-transmettre la catégorie de facettes sélectionnée par l’utilisateur.
  + La recherche basée sur la géolocalisation utilise un filtre pour transmettre les coordonnées de la localisation actuelle dans des applications de « recherche à proximité ». 
  + [Les filtres de sécurité](search-security-trimming-for-azure-search.md) transmettent des identificateurs de sécurité comme critères de filtre, une correspondance dans l’index servant de proxy pour les droits d’accès au document.

+ Effectuez une « recherche de numéros ». Les champs numériques sont récupérables et peuvent apparaître dans des résultats de la recherche. Cependant, ils ne peuvent pas faire l’objet d’une recherche (en texte intégral) individuelle. Si vous avez besoin de critères de sélection basés sur des données numériques, utilisez un filtre.

### <a name="alternative-methods-for-reducing-scope"></a>Autres méthodes de réduction de l’étendue de recherche

Si vous souhaitez restreindre vos résultats de recherche, les filtres ne sont pas le seul choix possible. Les solutions alternatives suivantes peuvent être plus appropriées selon votre objectif :

+ le paramètre de requête `searchFields` permet de restreindre une recherche à des champs spécifiques. Par exemple, si votre index comprend des champs distincts pour les descriptions en anglais et en espagnol, le paramètre searchFields vous permet de cibler les champs à utiliser pour une recherche en texte intégral. 

+ Le paramètre `$select` permet de spécifier les champs à inclure dans un jeu de résultats, ce qui a pour effet de réduire la réponse avant l’envoi de celle-ci à l’application appelante. Ce paramètre n’affine pas la requête et ne réduit pas la collection de documents. En revanche, si votre objectif est d’obtenir une plus petite réponse, ce paramètre constitue une option à envisager. 

Pour plus d’informations sur ces deux paramètres, voir [Rechercher des documents > Demande > Paramètres de requête](/rest/api/searchservice/search-documents#query-parameters).

## <a name="how-filters-are-executed"></a>Comment les filtres sont-ils exécutés ?

Au moment de la requête, un analyseur de filtre accepte les critères en entrée, convertit l’expression en expressions booléennes atomiques sous la forme d’une arborescence de filtres qui est ensuite évaluée sur les champs filtrables dans un index.

Le filtrage se produit en même temps que la recherche. Il permet de qualifier les documents à inclure dans le traitement en aval pour la récupération de documents et le scoring de leur pertinence. En association avec une chaîne de recherche, le filtre réduit efficacement l’ensemble de rappels de l’opération de recherche suivante. Utilisé seul (par exemple, lorsque la chaîne de requête est vide, où `search=*`), le critère de filtre est la seule entrée. 

## <a name="defining-filters"></a>Définition des filtres

Les filtres sont des expressions OData formulées dans la [syntaxe de filtre](search-query-odata-filter.md) prise en charge par la Recherche cognitive.

Vous pouvez spécifier un filtre pour chaque opération de **recherche**, mais le filtre lui-même peut inclure plusieurs champs, plusieurs critères et, si vous utilisez une fonction **ismatch**, plusieurs expressions de recherche en texte intégral. Dans une expression de filtre comportant plusieurs parties, vous pouvez spécifier des prédicats dans n’importe quel ordre (soumis aux règles de précédence de l’opérateur). Vous n’obtenez aucun gain sensible des performances si vous tentez de réorganiser les prédicats dans une séquence particulière.

L’une des limites inconditionnelles sur une expression de filtre est la limite de taille maximale de la demande. La demande entière, filtre inclus, peut être un maximum de 16 Mo pour la commande POST ou de 8 Ko pour la commande GET. Le nombre de clauses dans votre expression de filtre est également limité. Une règle empirique est que, si vous avez des centaines de clauses, vous risquez d’atteindre la limite. Nous vous recommandons de concevoir votre application de telle sorte qu’elle ne génère pas de filtres de taille illimitée.

Les exemples suivants illustrent des définitions de filtre prototypiques dans plusieurs API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrer les modèles d’utilisation

Les exemples suivants illustrent plusieurs modèles d’utilisation pour des scénarios de filtre. Pour d’autres idées, voir [Syntaxe d’expression OData > Exemples](./search-query-odata-filter.md#examples).

+ **$filter** autonome, sans chaîne de requête, utile lorsque l’expression de filtre est en mesure de qualifier complètement les documents d’intérêt. À défaut de chaîne de requête, il n’y a ni analyse lexicale ou linguistique, ni notation, ni classement. Notez que la chaîne de recherche comporte uniquement un astérisque, ce qui signifie « faire correspondre tous les documents ».

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ Combinaison de chaîne de requête et de **$filter**, où le filtre crée le sous-ensemble, et la chaîne de requête fournit les entrées de condition de recherche en texte intégral sur le sous-ensemble filtré. L’ajout de termes (théâtres à distance de marche) introduit des scores de recherche dans les résultats, où les documents qui correspondent le mieux aux termes sont mieux classés. Utiliser un filtre avec une chaîne de requête constitue le modèle d’utilisation le plus courant.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>Correspondance pour les hôtels mieux notés que les motels 4 ou 5 étoiles.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>Correspondance pour les termes « luxe » ou « haut de gamme » dans le champ « description » OU la catégorie correspondant précisément à « Luxe ».
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Language filters](search-filters-language.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Rendre filtrable un champ existant

Vous ne pouvez pas modifier des champs existants pour les rendre filtrables. À la place, vous devez ajouter un nouveau champ ou régénérer l’index. Pour plus d’informations sur la regénération d’un index ou la façon de remplir à nouveau des champs, consultez [Comment regénérer un index de Recherche cognitive Azure](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Notions de base concernant les filtres de texte

Les filtres de texte comparent les champs de chaîne aux chaînes littérales que vous fournissez dans le filtre : `$filter=Category eq 'Resort and Spa'`

Contrairement à la recherche en texte intégral, les filtres de texte n’appliquent aucune analyse lexicale ou césure de mots. Les comparaisons portent alors uniquement sur des correspondances exactes. Par exemple, supposons un champ *f* contenant les mots « Sunny day » (journée ensoleillée). `$filter=f eq 'sunny day'` sera une correspondance, mais pas `$filter=f eq 'Sunny'`. 

Les chaînes de texte respectent la casse. Il n’y a pas de conversion en minuscules des mots contenant des majuscules. Ainsi, la chaîne `$filter=f eq 'Sunny day'` ne permet pas de trouver « sunny day ».

### <a name="approaches-for-filtering-on-text"></a>Approches pour le filtrage de texte

| Approche | Description | Quand l’utiliser |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Une fonction qui compare un champ à une liste délimitée de chaînes. | Cette fonction est recommandée pour les [filtres de sécurité](search-security-trimming-for-azure-search.md) et pour tous les filtres dans lesquels plusieurs valeurs de texte brut doivent être comparées à un champ de chaîne. La fonction **search.in** est conçue pour fonctionner rapidement. Elle est donc beaucoup plus rapide qu’une comparaison explicite du champ à chaque chaîne à l’aide de `eq` et `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Fonction permettant de combiner des opérations de recherche en texte intégral avec des opérations de filtre strictement booléen dans une même expression de filtre. | Utilisez **search.ismatch** (ou son équivalent **search.ismatchscoring** pour le scoring) lorsque vous souhaitez utiliser plusieurs combinaisons de filtres et de recherches dans une seule demande. Vous pouvez également l’utiliser pour un filtre *contains* afin de filtrer sur une chaîne partielle figurant à l’intérieur d’une chaîne de plus grande taille. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Expression définie par l’utilisateur composée de champs, d’opérateurs et de valeurs. | Utilisez-la lorsque vous souhaitez rechercher des correspondances exactes entre un champ de chaîne et une valeur de chaîne. |

## <a name="numeric-filter-fundamentals"></a>Notions de base concernant les filtres numériques

Les champs numériques ne sont pas de type `searchable` (il n’est pas possible d’y effectuer une recherche) dans le contexte d’une recherche en texte intégral. Seules des chaînes peuvent faire l’objet d’une recherche en texte intégral. Par exemple, le terme de recherche 99,99 ne permet pas d’obtenir la liste des articles dont le prix est 99,99. Au lieu de cela, vous obtenez la liste des documents dont des champs de chaîne contiennent le nombre 99. Par conséquent, si vous avez des données numériques, l’hypothèse est que vous allez les utiliser pour des filtres, dont des plages, des facettes, des groupes, etc. 

Les documents contenant des champs numériques (prix, taille, référence, ID, etc.) fournissent ces valeurs dans les résultats de recherche si ces champs sont marqués en tant que `retrievable`. Ce qu’il importe de souligner ici, c’est que la recherche en texte intégral proprement dite ne s’applique pas aux champs de type numérique.

## <a name="next-steps"></a>Étapes suivantes

Tout d’abord, essayez l’**Explorateur de recherche** dans le portail pour soumettre des requêtes avec des paramètres de **$filter**. L’[index d’exemple immobilier](search-get-started-portal.md) fournit des résultats intéressants pour les requêtes filtrées suivantes lorsque vous les collez dans la barre de recherche :

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Pour utiliser d’autres exemples, voir [Syntaxe d’expression de filtre OData > Exemples](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Voir aussi

+ [Fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md)
+ [API REST de recherche de documents](/rest/api/searchservice/search-documents)
+ [Syntaxe de requête simple](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe de requête Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Types de données pris en charge](/rest/api/searchservice/supported-data-types)