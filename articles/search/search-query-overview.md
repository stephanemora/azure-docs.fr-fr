---
title: Types de requêtes
titleSuffix: Azure Cognitive Search
description: Découvrez les types de requêtes pris en charge dans Recherche cognitive, y compris le texte libre, le filtre, la saisie semi-automatique et les suggestions, la recherche basée sur la localisation, les requêtes système et la recherche de documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 234a0137f0a9487a56b3e0343eaea375d2f9a1af
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043012"
---
# <a name="querying-in-azure-cognitive-search"></a>Interrogation dans Recherche cognitive Azure

Recherche cognitive Azure offre un langage de requête riche pour prendre en charge un large éventail de scénarios, allant de la recherche en texte libre à des modèles de requête hautement spécifiés. Cet article décrit les demandes de requête et les types de requêtes que vous pouvez créer.

Dans Recherche cognitive, une requête est une spécification complète d’une opération aller-retour **`search`** , avec des paramètres qui définissent l’exécution de la requête et mettent en forme la réponse retournée. Les paramètres et les analyseurs déterminent le type de demande de requête. L’exemple de requête suivant est une requête de texte libre avec un opérateur booléen, utilisant [Rechercher des documents (API REST)](/rest/api/searchservice/search-documents) et ciblant la collection de documents [hotels-sample-index](search-get-started-portal.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Les paramètres utilisés lors de l’exécution de la requête sont les suivants :

+ **`queryType`** définit l’analyseur qui peut être l’[analyseur de requêtes simple par défaut](search-query-simple-examples.md) (optimal pour la recherche en texte intégral), ou l’[analyseur de requêtes complet Lucene](search-query-lucene-examples.md) utilisé pour les constructions de requêtes avancées, telles que les expressions régulières, la recherche de proximité, la recherche approximative et par caractères génériques et bien d’autres encore.

+ **`search`** fournit le critère de correspondance, généralement des termes ou expressions, avec ou sans opérateurs. Tout champ attribué en tant que champ avec *possibilité de recherche* dans le schéma d’index peut être utilisé avec ce paramètre.

+ **`searchFields`** limite l’exécution des requêtes à des champs spécifiques pouvant faire l’objet d’une recherche.

Paramètres utilisés pour former la réponse :

+ **`select`** spécifie les champs à retourner dans la réponse. Seuls les champs marqués comme *récupérables* dans l’index peuvent être utilisés dans une instruction select.

+ **`top`** retourne le nombre de documents les mieux correspondants spécifié. Dans cet exemple, seuls 10 correspondances sont retournées. Vous pouvez utiliser Top et Skip (non affichés) pour paginer les résultats.

+ **`count`** indique le nombre total de documents dans l’index complet, ce qui peut être supérieur à ce qui est retourné. 

+ **`orderby`** est utilisé si vous souhaitez trier les résultats en fonction d’une valeur, telle qu’une évaluation ou un emplacement. Dans le cas contraire, la valeur par défaut consiste à utiliser le score de pertinence pour classer les résultats. Un champ doit être attribué comme *pouvant être trié* pour être candidat à ce paramètre.

La liste ci-dessus est représentative mais non exhaustive. Pour obtenir la liste complète des paramètres d’une demande de requête, consultez [Rechercher des documents (API REST)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Types de requête

Avec quelques exceptions notables, une demande de requête effectue une itération sur les index inversés structurés pour des analyses rapides, où une correspondance peut être trouvée dans un champ et dans n’importe quel nombre de documents de recherche. Dans Recherche cognitive, la principale méthodologie de recherche des correspondances est la recherche en texte intégral ou les filtres, mais vous pouvez également implémenter d’autres expériences de recherche connues comme la saisie semi-automatique ou la recherche sur la localisation. Le reste de cet article résume les requêtes dans Recherche cognitive et fournit des liens vers des informations et des exemples supplémentaires.

## <a name="full-text-search"></a>Recherche en texte intégral

Si votre application de recherche inclut une zone de recherche qui collecte des entrées de termes, la recherche en texte intégral est probablement l’opération de requête qui permet cette expérience. La recherche en texte intégral accepte les termes ou expressions passés dans un paramètre **`search`** dans tous les champs *pouvant faire l’objet d’une recherche* de votre index. Les opérateurs booléens facultatifs dans la chaîne de requête peuvent spécifier des critères d’inclusion ou d’exclusion. L’analyseur simple et l’analyseur complet prennent tous deux en charge la recherche en texte intégral.

Dans Recherche cognitive, la recherche en texte intégral repose sur le moteur de requête Apache Lucene. Dans une recherche en texte intégral, les chaînes de requête font l’objet d’une analyse lexicale pour améliorer l’efficacité des analyses. L’analyse comprend le respect de la casse de tous les termes, la suppression des mots vides comme « le/la » et la réduction des termes aux formes racines primitives. L’analyseur par défaut est Lucene standard.

Lorsque des termes correspondants sont trouvés, le moteur de requête reconstitue un document de recherche contenant la correspondance à l’aide de la clé ou de l’ID du document pour assembler des valeurs de champ, classe les documents par ordre de pertinence, puis retourne les 50 premiers résultats (par défaut) dans la réponse, ou un autre nombre si vous avez spécifié **`top`** .

Si vous implémentez la recherche en texte intégral, la compréhension de la manière dont votre contenu est segmenté en jetons vous aidera à déboguer les anomalies de requête. Les requêtes sur des chaînes comportant des traits d’union ou des caractères spéciaux peuvent nécessiter l’utilisation d’un analyseur autre que le moteur Lucene standard par défaut pour s’assurer que l’index contient les jetons appropriés. Vous pouvez remplacer l’analyseur par défaut en choisissant des [analyseurs de langage](index-add-language-analyzers.md#language-analyzer-list) ou des [analyseurs spécialisés](index-add-custom-analyzers.md#AnalyzerTable) qui modifient les paramètres d’analyse lexicale. Un analyseur [mot clé](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) traite par exemple tout le contenu d’un champ comme un jeton unique. Ceci est utile pour les données comme les codes postaux, les ID et certains noms de produit. Pour plus d’informations, consultez [Recherche de termes partiels et modèles avec des caractères spéciaux](search-query-partial-matching.md).

Si vous prévoyez une utilisation intensive d’opérateurs booléens, ce qui est plus probable dans les index contenant des blocs de texte volumineux (un champ de contenu ou des descriptions longues), veillez à tester les requêtes avec le paramètre **`searchMode=Any|All`** pour évaluer l’impact de ce paramètre sur la recherche booléenne.

## <a name="autocomplete-and-suggested-queries"></a>Requêtes de saisie semi-automatique et suggérées

[La saisie semi-automatique ou les requêtes suggérées](search-autocomplete-tutorial.md) sont des alternatives à **`search`** qui déclenchent des demandes de requête successives basées sur des entrées de chaîne partielles (après chaque caractère) dans une expérience de recherche en cours de frappe. Vous pouvez utiliser les paramètres **`autocomplete`** et **`suggestions`** conjointement ou séparément, comme décrit dans [ce tutoriel](tutorial-csharp-type-ahead-and-suggestions.md), mais vous ne pouvez pas les utiliser avec **`search`** . Les termes complets et les requêtes suggérées sont dérivés du contenu de l’index. Le moteur ne retourne jamais une chaîne ou une suggestion qui n’existe pas dans votre index. Pour plus d’informations, consultez [Saisie semi-automatique (API REST)](/rest/api/searchservice/autocomplete) et [Suggestions (API REST)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Filtrer la recherche

Les filtres sont largement utilisés dans les applications qui incluent la fonctionnalité Recherche cognitive. Dans les pages d’application, les filtres sont souvent visualisés comme des facettes dans les structures de navigation par lien pour permettre un filtrage défini par l’utilisateur. Les filtres sont également utilisés en interne pour exposer des tranches de contenu indexé. Par exemple, vous pouvez initialiser une page de recherche à l’aide d’un filtre sur une catégorie de produit, ou une langue si un index contient des champs en anglais et en français.

Vous pouvez également avoir besoin de filtres pour appeler un formulaire de requête spécialisé, comme décrit dans le tableau suivant. Vous pouvez utiliser un filtre avec une recherche non spécifiée ( **`search=*`** ) ou avec une chaîne de requête incluant des termes, des expressions, des opérateurs et des modèles.

| Filtrer un scénario | Description |
|-----------------|-------------|
| Filtres de plage | Dans Recherche cognitive Azure, les requêtes de plage sont créées à l’aide du paramètre de filtre. Pour plus d’informations et d’exemples, consultez [Exemple de filtre de plage](search-query-simple-examples.md#example-5-range-filters). |
| Recherche sur la localisation | Si un champ pouvant faire l’objet d’une recherche est de [type EDM.GeographyPoint](/rest/api/searchservice/supported-data-types), vous pouvez créer une expression de filtre « rechercher à proximité » ou des contrôles de recherche basés sur une carte. Les champs qui définissent la recherche basée sur la localisation contiennent des coordonnées. Pour plus d’informations et pour obtenir un exemple, consultez [Exemple de recherche sur la localisation](search-query-simple-examples.md#example-6-geo-search). |
| Navigation à facettes | Une structure de navigation à facettes devient instrumentale dans une navigation définie par l’utilisateur lorsque vous appelez un filtre en réponse à un événement `onclick` sur une facette. Par conséquent, les facettes et les filtres vont de pair. Si vous ajoutez une navigation à facettes, vous aurez besoin de filtres pour compléter l’expérience. Pour plus d’informations, consultez [Comment créer un filtre de facette](search-filters-facets.md). |

> [!NOTE]
> Le texte utilisé dans une expression de filtre n’est pas analysé lors du traitement de la requête. L’entrée de texte est supposée être un modèle textuel de caractères respectant la casse et qui correspond ou non au filtre. Les expressions de filtre sont construites à l’aide de la [syntaxe OData](query-odata-filter-orderby-syntax.md) et passées dans un paramètre **`filter`** dans tous les champs *filtrables* de votre index. Pour plus d’informations, consultez [Filtres dans la Recherche cognitive Azure](search-filters.md).

## <a name="document-look-up"></a>Recherche de document

Contrairement aux formulaires de requête décrits précédemment, cette recherche récupère un seul [document de recherche par ID](/rest/api/searchservice/lookup-document), sans recherche ni analyse d’index correspondante. Seul le document est demandé et retourné. Lorsqu’un utilisateur sélectionne un élément dans les résultats de la recherche, la récupération du document et le remplissage d’une page de détails avec des champs constituent une réponse classique, et une recherche de document est l’opération qui la prend en charge.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Recherche avancée : approximative, caractère générique, proximité, expression régulière

Un formulaire de requête avancé dépend de l’analyseur Lucene complet et d’opérateurs qui déclenchent un comportement de requête spécifique.

| Type de requête | Usage | Exemples et informations complémentaires |
|------------|--------|------------------------------|
| [Recherche par champ](query-lucene-syntax.md#bkmk_fields) | Paramètre **`search`** , **`queryType=full`**  | Crée une expression de requête composite ciblant un champ unique. <br/>[Exemple de recherche par champ](search-query-lucene-examples.md#example-1-fielded-search) |
| [recherche approximative](query-lucene-syntax.md#bkmk_fuzzy) | Paramètre **`search`** , **`queryType=full`** | Recherche les termes ayant une construction ou une orthographe similaire. <br/>[Exemple de recherche approximative](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [recherche de proximité](query-lucene-syntax.md#bkmk_proximity) | Paramètre **`search`** , **`queryType=full`** | Recherche les termes proches les uns des autres dans un document. <br/>[Exemple de recherche de proximité](search-query-lucene-examples.md#example-3-proximity-search) |
| [promotion de termes](query-lucene-syntax.md#bkmk_termboost) | Paramètre **`search`** , **`queryType=full`** | Élève le rang d’un document qui contient le terme de promotion, par rapport aux documents qui ne contiennent pas ce terme. <br/>[Exemple de promotion de termes](search-query-lucene-examples.md#example-4-term-boosting) |
| [recherche d’expression régulière](query-lucene-syntax.md#bkmk_regex) | Paramètre **`search`** , **`queryType=full`** | Exécute la recherche à partir du contenu d’une expression régulière. <br/>[Exemple de recherche d’expression régulière](search-query-lucene-examples.md#example-5-regex) |
|  [recherche par préfixe ou caractères génériques](query-lucene-syntax.md#bkmk_wildcard) | Paramètre **`search`** avec **_`~`_* ou **`?`** , **`queryType=full`**| Exécute la recherche à partir d’un préfixe et d’un tilde (`~`) ou d’un caractère unique (`?`). <br/>[Exemple de recherche par caractères génériques](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation détaillée de l’implémentation des requêtes, passez en revue les exemples de chaque syntaxe. Si vous ne connaissez pas la recherche en texte intégral, il est judicieux d’examiner attentivement les fonctions du moteur de requête.

+ [Exemples de requête simple](search-query-simple-examples.md)
+ [Exemples de syntaxe de requête Lucene pour créer des requêtes avancées](search-query-lucene-examples.md)
+ [Fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md)