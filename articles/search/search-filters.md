---
title: Filtres d’étendue des résultats de recherche dans un index - Recherche Azure
description: Filtrez par identité de sécurité d’utilisateur, langue, emplacement géographique ou valeurs numériques pour réduire les résultats de recherche des requêtes effectuées dans Recherche Azure, service de recherche dans le cloud hébergé sur Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1871fee2734d347ff54d6aa70d90d1c28bd1f6f1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597283"
---
# <a name="filters-in-azure-search"></a>Filtres dans Recherche Azure 

Un *filtre* établit des critères pour la sélection des documents sur lesquels doit porter une requête de Recherche Azure. Une recherche non filtrée inclut tous les documents figurant dans l’index. Un filtre détermine l’étendue d’une requête de recherche en limitant celle-ci à un sous-ensemble de documents. Par exemple, un filtre peut restreindre une recherche en texte intégral à des produits d’une marque ou d’une couleur spécifiques, commercialisés à des niveaux de prix supérieurs à un seuil défini.

Certaines expériences de recherche imposent des exigences de filtre dans le cadre de leur implémentation, mais vous pouvez utiliser des filtres à chaque fois que vous souhaitez limiter la recherche à l’aide de critères *basés sur des valeurs* (par exemple, en définissant l’étendue de la recherche sur le type de produit « livres » de la catégorie « non-fiction » publiés par « Simon & Schuster »).

Si, au lieu de cela, votre objectif est une recherche ciblée sur des *structures* de données spécifiques (par exemple, en définissant l’étendue de la recherche sur un champ contenant des avis de clients), il existe des méthodes alternatives décrites ci-dessous.

## <a name="when-to-use-a-filter"></a>Quand utiliser un filtre

Certains filtres sont fondamentaux pour diverses expériences de recherche. C’est notamment le cas des filtres de « recherche à proximité », de navigation par facettes et de sécurité, qui montrent uniquement les documents qu’un utilisateur est autorisé à afficher. Si vous implémentez l’une de ces expériences, un filtre est requis. C’est le filtre associé à la requête de recherche qui fournit les coordonnées de géolocalisation, la catégorie de facettes sélectionnée par l’utilisateur, ou l’ID de sécurité du demandeur.

Voici des exemples de scénarios :

1. Utilisez un filtre pour découper votre index sur la base de valeurs de données figurant dans l’index. À partir d’un schéma donné, avec une ville, un type de logement et des commodités, vous pouvez créer un filtre permettant de sélectionner explicitement les documents correspondant à vos critères (par exemple, Marseille, appartement, front de mer). 

   Une recherche en texte intégral portant sur les mêmes entrées produit souvent des résultats similaires, mais un filtre est plus précis car il requiert une correspondance exacte entre la condition de filtre et le contenu de votre index. 

2. Utilisez un filtre si l’expérience de recherche intègre une condition de filtre :

   * La [navigation par facettes](search-faceted-navigation.md) utilise un filtre pour re-transmettre la catégorie de facettes sélectionnée par l’utilisateur.
   * La recherche basée sur la géolocalisation utilise un filtre pour transmettre les coordonnées de la localisation actuelle dans des applications de « recherche à proximité ». 
   * Les filtres de sécurité transmettent des identificateurs de sécurité en tant que critères de filtre, où une correspondance dans l’index sert de proxy pour les droits d’accès au document.

3. Utilisez un filtre si vous souhaitez appliquer des critères de recherche sur un champ numérique. 

   Les champs numériques sont récupérables dans le document et peuvent apparaître dans des résultats de recherche, mais ils ne peuvent pas faire l’objet individuellement d’une recherche en texte intégral. Si vous avez besoin de critères de sélection basés sur des données numériques, utilisez un filtre.

### <a name="alternative-methods-for-reducing-scope"></a>Autres méthodes de réduction de l’étendue de recherche

Si vous souhaitez restreindre vos résultats de recherche, les filtres ne sont pas le seul choix possible. Les solutions alternatives suivantes peuvent être plus appropriées selon votre objectif :

 + Le paramètre de requête `searchFields` permet de focaliser une recherche sur des champs spécifiques. Par exemple, si votre index comprend des champs distincts pour les descriptions en anglais et en espagnol, le paramètre searchFields vous permet de cibler les champs à utiliser pour une recherche en texte intégral. 

+ Le paramètre `$select` permet de spécifier les champs à inclure dans un jeu de résultats, ce qui a pour effet de réduire la réponse avant l’envoi de celle-ci à l’application appelante. Ce paramètre ne pas affiner la requête ou réduire la collection de documents, mais si votre objectif d’est une réponse plus petits, ce paramètre est une option à prendre en compte. 

Pour plus d’informations sur ces deux paramètres, voir [Rechercher des documents > Demande > Paramètres de requête](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>Comment les filtres sont exécutés.

Au moment de la requête, un analyseur de filtre accepte les critères en tant qu’entrée, convertit l’expression en expressions booléennes atomiques représentées sous forme d’arborescence, puis évalue l’arbre de filtre sur les champs filtrables dans un index.

Le filtrage se produit en tandem avec la recherche, qualifier les documents à inclure dans le traitement en aval pour la récupération de document et de la notation de leur pertinence. Associé à une chaîne de recherche, le filtre réduit efficacement l’ensemble de rappel de l’opération de recherche suivante. Utilisé seul (par exemple, lorsque la chaîne de requête est vide, où `search=*`), le critère de filtre est la seule entrée. 

## <a name="defining-filters"></a>Définition des filtres

Les filtres sont des expressions OData, articulées à l’aide d’un [sous-ensemble de la syntaxe OData V4 prise en charge dans Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Vous pouvez spécifier un filtre pour chaque **recherche** opération, mais le filtre lui-même peut inclure plusieurs champs, plusieurs critères, et si vous utilisez un **ismatch** (fonction), plusieurs expressions de recherche en texte intégral. Dans une expression de filtre comportant plusieurs parties, vous pouvez spécifier des prédicats dans n’importe quel ordre (soumis aux règles de priorité des opérateurs). Vous n’obtenez aucun gain sensible des performances si vous tentez de réorganiser les prédicats dans une séquence particulière.

Une des limites d’une expression de filtre est la limite de taille maximale de la demande. La demande entière, filtre inclus, peut être un maximum de 16 Mo pour la commande POST ou de 8 Ko pour la commande GET. Il existe également une limite sur le nombre de clauses dans votre expression de filtre. Une règle empirique est que, si vous avez des centaines de clauses, vous risquez d’atteindre la limite. Nous vous recommandons de concevoir votre application de telle sorte qu’elle ne génère pas de filtres de taille illimitée.

Les exemples suivants illustrent des définitions de filtre prototypiques dans plusieurs API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrer les modèles d’utilisation

Les exemples suivants illustrent plusieurs modèles d’utilisation pour les scénarios de filtre. Pour d’autres idées, voir [Syntaxe d’expression OData > Exemples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ **$filter** autonome, sans chaîne de requête, utile lorsque l’expression de filtre est en mesure de qualifier complètement les documents d’intérêt. À défaut de chaîne de requête, il n’y a ni analyse lexicale ou linguistique, ni notation, ni classement. Notez que la chaîne de recherche est simplement un astérisque, ce qui signifie « faire correspondre tous les documents ».

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinaison de chaîne de requête et de **$filter**, où le filtre crée le sous-ensemble, et la chaîne de requête fournit les entrées de condition de recherche en texte intégral sur le sous-ensemble filtré. À l’aide d’un filtre avec une chaîne de requête est le modèle d’utilisation courant.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Requêtes composées, séparées par « OR » (ou), chacune avec ses propres critères de filtre (par exemple, « beagle » dans « chien » ou « siamois » dans « chat »). Les expressions sont combinées avec `or` sont évaluées individuellement, avec l’union de documents correspondant à chaque expression renvoyée dans la réponse. Ce modèle d’utilisation est obtenu via la `search.ismatchscoring` (fonction). Vous pouvez également utiliser la version sans notation, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Il est également possible de combiner la recherche en texte intégral via `search.ismatchscoring` avec des filtres à l’aide de `and` au lieu de `or`, mais ce n’est fonctionnellement équivalent à l’aide la `search` et `$filter` paramètres dans une requête de recherche. Par exemple, les deux requêtes suivantes produisent le même résultat :

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Pour obtenir des instructions complètes sur des cas d’usage spécifiques, consultez les articles suivants :

+ [Filtres de facette](search-filters-facets.md)
+ [Filtres de langage](search-filters-language.md)
+ [Filtrage de sécurité](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Conditions requises des champs pour le filtrage

Dans l’API REST, filtrable est *sur* par défaut pour les champs simples. Les champs filtrables augmentent la taille de l’index. Veillez à définir `"filterable": false` pour les champs que vous ne prévoyez pas réellement d’utiliser dans un filtre. Pour plus d’informations sur les paramètres des définitions de champ, voir [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Créer un index).

Dans le Kit de développement logiciel (SDK) .NET, la propriété filterable (filtrable) est *désactivée* par défaut. Vous pouvez rendre un champ filtrables en définissant le [IsFilterable propriété](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) correspondantes [champ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) objet `true`. Vous pouvez également faire de façon déclarative à l’aide de la [IsFilterable attribut](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). Dans l’exemple ci-dessous, l’attribut est défini sur le `BaseRate` propriété d’une classe de modèle qui correspond à la définition d’index.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Rendre un champ existant filtrables

Vous ne pouvez pas modifier des champs existants pour les rendre filtrable. Au lieu de cela, vous devez ajouter un nouveau champ, ou reconstruire l’index. Pour plus d’informations sur la reconstruction d’un index ou de nouveau remplissage de champs, consultez [comment reconstruire un index Azure Search](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Notions de base concernant les filtres de texte

Filtres de texte correspondent aux champs de chaîne par rapport à des chaînes littérales que vous fournissez dans le filtre. Contrairement à la recherche en texte intégral, n’est aucune analyse lexicale ou la césure de mots pour les filtres de texte, les comparaisons sont donc pour les correspondances exactes uniquement. Par exemple, supposons un champ *f* contient « journée ensoleillée », `$filter=f eq 'Sunny'` ne correspond pas, mais `$filter=f eq 'sunny day'` sera. 

Les chaînes de texte respectent la casse. Il n’existe aucun minuscules des mots en majuscule : `$filter=f eq 'Sunny day'` ne trouvera pas « journée ensoleillée ».

### <a name="approaches-for-filtering-on-text"></a>Approches pour le filtrage sur du texte

| Approche | Description  | Quand utiliser | 
|----------|-------------|-------------|
| [search.in](query-odata-filter-orderby-syntax.md) | Une fonction qui correspond à un champ par rapport à une liste délimitée de chaînes. | Recommandé pour [filtres de sécurité](search-security-trimming-for-azure-search.md) et pour tous les filtres où plusieurs valeurs de texte brut doivent être mis en correspondance avec un champ de chaîne. Le **search.in** (fonction) est conçue pour aller plus vite et est beaucoup plus rapide que la comparaison explicite le champ par rapport à chaque chaîne à l’aide `eq` et `or`. | 
| [search.ismatch](query-odata-filter-orderby-syntax.md) | Fonction permettant de combiner des opérations de recherche en texte intégral avec des opérations de filtre strictement booléen dans une même expression de filtre. | Utilisez **search.ismatch** (ou son équivalent de notation, **search.ismatchscoring**) lorsque vous souhaitez que plusieurs combinaisons de filtre de recherche dans une seule demande. Vous pouvez également l’utiliser pour un filtre *contains* afin de filtrer sur une chaîne partielle figurant à l’intérieur d’une chaîne de plus grande taille. |
| [$filter=field operator string](query-odata-filter-orderby-syntax.md) | Expression définie par l’utilisateur composée de champs, d’opérateurs et de valeurs. | Utilisez-le lorsque vous souhaitez rechercher des correspondances exactes entre un champ de chaîne et une valeur de chaîne. |

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

Pour utiliser d’autres exemples, voir [Syntaxe d’expression de filtre OData > Exemples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="see-also"></a>Voir aussi

+ [Fonctionnement de la recherche en texte intégral dans la Recherche Azure](search-lucene-query-architecture.md)
+ [API REST de recherche de documents](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Types de données prises en charge](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
