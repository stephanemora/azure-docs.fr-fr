---
title: Notions fondamentales concernant les requêtes dans Recherche Azure | Microsoft Docs
description: Principes de base pour la création d’une requête de recherche dans Recherche Azure, avec utilisation de paramètres pour filtrer, sélectionner et trier les résultats.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366444"
---
# <a name="query-fundamentals-in-azure-search"></a>Notions fondamentales concernant les requêtes dans Recherche Azure

Une définition de requête dans Recherche Azure est une spécification complète d’une requête qui inclut les éléments suivants : point de terminaison d’URL de service, index cible, api-key à utiliser pour authentifier la requête, api-version et une chaîne de requête. 

La chaîne de requête est composée de paramètres définis dans l’[API Rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents). Le plus important est le paramètre **search=**, qui peut être non défini (`search=*`) mais est le plus souvent composé de termes, d’expressions et d’opérateurs comme dans l’exemple suivant :

```
"search": "seattle townhouse +\"lake\""
```

D’autres paramètres sont utilisés pour diriger le traitement des requêtes ou pour améliorer la réponse. Les paramètres servent entre autres à limiter la recherche à des champs spécifiques, à définir un mode de recherche afin de moduler la compensation précision/rappel, et à ajouter un comptage afin de pouvoir effectuer le suivi des résultats. 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

Bien que la définition de requête soit fondamentale, votre schéma d’index est tout aussi important dans la façon dont il spécifie les opérations autorisées au champ par champ. Pendant le développement de l’index, les attributs sur les champs déterminent les opérations autorisées. Par exemple, pour être éligible à la recherche en texte intégral et inclus dans les résultats de recherche, un champ doit être marqué à la fois comme *récupérable* et *recherchable*.

Au moment de la requête, l’exécution s’effectue toujours par rapport à un seul index, authentifié à l’aide d’une clé d’API (api-key) fournie dans la requête. Vous ne pouvez pas joindre des index ou créer des structures de données personnalisées ou temporaires en tant que requête cible.  

Les résultats de la requête sont envoyés sous forme de documents JSON dans l’API REST, mais si vous utilisez des API .NET, la sérialisation est intégrée. Vous pouvez mettre en forme les résultats en définissant des paramètres sur la requête, en sélectionnant des champs spécifiques pour le résultat

Pour résumer, la substance de la demande de requête spécifie les opérations et l’étendue : quels champs inclure dans la recherche, quels champs inclure dans le jeu de résultats, s’il faut trier ou filtrer, et ainsi de suite. Non spécifiée, une requête s’exécute sur tous les champs recherchables en tant qu’opération de recherche de texte intégral et retourne un jeu de résultats sans score dans un ordre arbitraire.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Types de requêtes : rechercher et filtrer

Recherche Azure propose de nombreuses options pour créer des requêtes extrêmement performantes. Les deux types principaux de requête que vous allez utiliser sont `search` et `filter`. 

+ Les requêtes `search` recherchent un ou plusieurs termes dans tous les champs *recherchables* de votre index et fonctionnent à l’instar des moteurs de recherche Google ou Bing. Les exemples de l’introduction utilisent le paramètre `search`.

+ Les requêtes `filter` évaluent une expression booléenne dans tous les champs *filtrables* d’un index. Contrairement à `search`, une requête `filter` établit une correspondance avec le contenu exact d’un champ, y compris la casse dans les champs de type chaîne.

Vous pouvez utiliser search et filter conjointement ou séparément. Un filtre autonome, sans chaîne de requête, est utile quand l’expression de filtre est en mesure de qualifier complètement les documents d’intérêt. À défaut de chaîne de requête, il n’y a ni analyse lexicale ou linguistique, ni notation, ni classement. Vous pouvez remarquer que la chaîne de recherche est vide.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

Utilisés conjointement, le filtre est d’abord appliqué à la totalité de l’index et la recherche est effectuée sur les résultats du filtre. Les filtres peuvent donc être utiles pour améliorer les performances des requêtes, puisqu’ils limitent le nombre de documents que devra traiter la requête de recherche.

La syntaxe des expressions de filtre est un sous-ensemble du [langage de filtre OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Pour les requêtes de recherche, vous pouvez utiliser la [syntaxe simplifiée](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) ou la [syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) qui sont présentées ci-dessous.


## <a name="choose-a-syntax-simple-or-full"></a>Choisir une syntaxe : simple ou complète

Le service Recherche Azure repose sur Apache Lucene et vous offre le choix entre deux analyseurs de requêtes pour la gestion des requêtes classiques et spécialisées. Les requêtes de recherche classiques sont formulées à l’aide de la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) par défaut. Cette syntaxe prend en charge un certain nombre d’opérateurs de recherche courants, notamment les opérateurs AND, OR, NOT, les expressions, les suffixes et les opérateurs de priorité.

La [syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), activée quand vous ajoutez **queryType=full** à la requête, expose le langage de requête expressif et largement adopté développé dans le cadre d’[Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). L’utilisation de cette syntaxe de requête autorise les requêtes spécialisées :

+ [Requêtes sur des champs](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [recherche approximative](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [recherche de proximité](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [promotion de termes](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [recherche d’expression régulière](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [recherche par caractères génériques](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

Les opérateurs booléens sont essentiellement les mêmes dans les deux syntaxes, avec des formats supplémentaires dans la syntaxe Lucene complète :

+ [Opérateurs booléens dans la syntaxe simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [Opérateurs booléens dans la syntaxe Lucene complète](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>Éléments obligatoires et facultatifs

Lorsque vous soumettez des demandes de recherche à Azure Search, vous pouvez spécifier plusieurs paramètres avec les mots que vous tapez dans la zone de recherche de votre application. Ces paramètres de requête vous permettent d’obtenir un contrôle plus étroit de [l’expérience de recherche en texte intégral](search-lucene-query-architecture.md).

Les éléments obligatoires dans une demande de requête incluent les composants suivants :

+ Point de terminaison de service et collection de documents d’index, exprimés ici sous forme d’URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ Version de l’API (REST uniquement), exprimée sous la forme **api-version=**
+ Clé d’API de requête ou administration, exprimée sous la forme **api-key=**
+ Chaîne de requête exprimée sous la forme **search=**, qui peut être non spécifiée si vous souhaitez effectuer une recherche vide. Vous pouvez aussi envoyer juste une expression de filtre **$filter=**.
+ **queryType=**, simple ou full, qui peut être omis si vous souhaitez utiliser la syntaxe simple par défaut.

Tous les autres paramètres de recherche sont facultatifs.

## <a name="apis-and-tools-for-testing"></a>API et outils de test

Le tableau suivant liste les API et les approches basées sur des outils pour envoyer des requêtes.

| Méthodologie | Description |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Client que vous pouvez utiliser pour interroger un index Recherche Azure.  <br/>[En savoir plus.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Rechercher des documents (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Méthodes GET ou POST sur un index, avec paramètres de requête pour une entrée supplémentaire.  |
| [Fiddler, Postman ou autre outil de test HTTP](search-fiddler.md) | Explique comment configurer un en-tête et un corps de requête pour l’envoi de requêtes à Recherche Azure.  |
| [Explorateur de recherche dans le portail Azure](search-explorer.md) | Fournit une barre de recherche et des options pour les sélections d’index et de version d’API. Les résultats sont retournés sous forme de documents JSON. <br/>[En savoir plus.](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>Gérer les résultats de recherche

Vous pouvez utiliser les paramètres de la requête pour structurer le jeu de résultats des manières suivantes :

+ Limitation ou traitement par lot du nombre de documents dans les résultats (50 par défaut)
+ Sélection des champs à inclure dans les résultats
+ Définition d’un ordre de tri
+ Ajout de la mise en surbrillance des correspondances pour attirer l’attention sur les termes correspondants dans le corps des résultats de recherche

### <a name="tips-for-unexpected-results"></a>Conseils en cas de résultats inattendus

Parfois, la substance et non la structure de résultats est inattendue. Quand les résultats de requête ne sont pas à la hauteur de vos attentes, vous pouvez essayer ces modifications de requête pour voir si les résultats s’améliorent :

+ Remplacez `searchMode=any` (valeur par défaut) par `searchMode=all` pour exiger des correspondances sur tous les critères plutôt que sur un seul d’entre eux. Cela s’applique particulièrement quand des opérateurs booléens sont inclus dans la requête.

+ Changez la technique de requête si l’analyse lexicale ou du texte est nécessaire, mais que le type de requête exclut tout traitement linguistique. Dans la recherche en texte intégral, l’analyse lexicale ou du texte corrige automatiquement les fautes d’orthographe, les formes singulier-pluriel des noms, et même les noms ou les verbes irréguliers. Pour certaines requêtes telles que la recherche approximative ou par caractères génériques, l’analyse de texte ne fait pas partie du pipeline d’analyse de requête. Dans certains scénarios, des expressions régulières ont été utilisées pour contourner ce problème. 

### <a name="paging-results"></a>Résultats de pagination
Azure Search facilite l’implémentation de la pagination des résultats de recherche. À l’aide des paramètres `top` et `skip`, vous pouvez facilement émettre des demandes de recherche qui vous permettent de recevoir l’ensemble total des résultats de recherche dans des sous-ensembles gérables, ordonnés qui permettent de bonnes pratiques de recherche dans l’interface utilisateur. Lors de la réception de ces sous-ensembles de résultats plus petits, vous pouvez également recevoir le nombre de documents dans l’ensemble total des résultats de la recherche.

Pour plus d’informations sur la pagination des résultats de recherche, consultez l’article [Navigation dans les résultats de recherche d’Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Classement des résultats
Lors de la réception des résultats d’une requête de recherche, vous pouvez demander qu’Azure Search produise les résultats classés par valeurs dans un champ spécifique. Par défaut, Azure Search classe les résultats en fonction du rang du résultat de la recherche de chaque document, qui est dérivé de la méthode [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Si vous souhaitez qu’Azure Search retourne les résultats en les classant avec une valeur autre que le résultat de la recherche, vous pouvez utiliser le paramètre de recherche `orderby` . Vous pouvez spécifier la valeur du paramètre `orderby` pour inclure les noms de champ et les appels à la [fonction `geo.distance()`](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) pour les valeurs géospatiales. Chaque expression peut être suivie par `asc` pour indiquer que les résultats sont demandés dans l’ordre croissant, et par `desc` pour indiquer que les résultats sont demandés dans l’ordre décroissant. Le classement par défaut est l’ordre croissant.


### <a name="hit-highlighting"></a>Mise en surbrillance des correspondances
Dans Azure Search, vous pouvez mettre facilement en évidence la partie exacte des résultats de recherche qui correspondent à la requête de recherche en utilisant les paramètres `highlight`, `highlightPreTag` et `highlightPostTag`. Vous pouvez spécifier les champs *utilisables dans une recherche* dont le texte correspondant à la requête doit être mis en évidence ainsi que les balises de chaîne exactes à ajouter au début et à la fin du texte correspondant retourné par le service Recherche Azure.

## <a name="see-also"></a>Voir aussi

+ [Fonctionnement de la recherche en texte intégral dans la Recherche Azure (architecture d’analyse de requête)](search-lucene-query-architecture.md)
+ [Navigateur de recherche](search-explorer.md)
+ [Guide pratique pour interroger dans .NET](search-query-dotnet.md)
+ [Guide pratique pour interroger dans REST](search-query-rest-api.md)
