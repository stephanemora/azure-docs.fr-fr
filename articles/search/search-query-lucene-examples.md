---
title: Exemples de requêtes Lucene pour la Recherche Azure | Microsoft Docs
description: Syntaxe de requête Lucene pour la recherche approximative, la recherche de proximité, l’amélioration de termes, la recherche d’expressions régulières et la recherche par caractères génériques dans le service Recherche Azure.
author: LiamCa
manager: pablocas
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: liamca
ms.openlocfilehash: d90a7b2d12a147b8020abbd51ef055f0e70471fb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365426"
---
# <a name="lucene-syntax-query-examples-for-building-advanced-queries-in-azure-search"></a>Exemples de syntaxe de requête Lucene pour créer des requêtes avancées dans Recherche Azure
Lors de la construction de requêtes pour Recherche Azure, vous pouvez remplacer l’[analyseur de requêtes simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) par défaut par l’[analyseur de requêtes Lucene dans Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) afin de formuler des définitions de requêtes spécialisées et avancées. 

L’analyseur de requêtes Lucene prend en charge des constructions de requêtes plus complexes, telles que des requêtes portant sur des champs, la recherche approximative et par caractères génériques, la recherche de proximité, la promotion de termes et la recherche d’expression régulière. La puissance supplémentaire implique des exigences de traitement supplémentaires. Dans cet article, vous pouvez parcourir des exemples décrivant les opérations de requête disponibles lors de l’utilisation de la syntaxe complète.

> [!Note]
> Une grande partie des constructions de requêtes spécialisées activées par le biais de la syntaxe de requête Lucene complète ne sont pas soumises à une [analyse du texte](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), ce qui peut être surprenant si vous prévoyez une recherche de radical ou une lemmatisation. L’analyse lexicale est effectuée uniquement sur des termes complets (requête sur un terme ou une expression). Les types de requête avec des termes incomplets (requête de préfixe, par caractères génériques, d’expression régulière, approximative) sont ajoutés directement à l’arborescence de requête, en ignorant la phase d’analyse. La seule transformation effectuée sur les termes de requête incomplets est l’utilisation de minuscules. 
>

## <a name="formulate-requests-in-postman"></a>Formuler des requêtes dans Postman

Les exemples suivants utilisent un index de recherche NYC Jobs composé de postes à pourvoir sur la base d’un jeu de données fourni par l’initiative [City of New York OpenData](https://nycopendata.socrata.com/). Ces données ne doivent pas être considérées comme étant à jour ou complètes. L’index se trouve sur un service de bac à sable fourni par Microsoft, ce qui signifie que vous n’avez pas besoin d’abonnement Azure ni de Recherche Azure pour essayer ces requêtes.

En revanche, vous avez besoin de Postman ou d’un outil équivalent pour émettre la requête HTTP sur GET. Pour plus d’informations, consultez [Tester avec les clients REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Définir l’en-tête de requête

1. Dans l’en-tête de requête, affectez la valeur `application/json` à **Content-Type**.

2. Ajoutez un **api-key** et affectez-lui cette chaîne : `252044BE3886FE4A8E3BAA4F595114BB`. Il s’agit d’une clé de requête pour le service de recherche de bac à sable qui héberge l’index NYC Jobs.

Une fois que vous avez spécifié l’en-tête de requête, vous pouvez le réutiliser pour toutes les requêtes dans cet article, en remplaçant uniquement la chaîne **search=**. 

  ![En-tête de requête Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Définir l’URL de requête

La requête est une commande GET accompagnée d’une URL contenant le point de terminaison Recherche Azure et la chaîne de recherche.

  ![En-tête de requête Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

L’URL est composée des éléments suivants :

+ **`https://azs-playground.search.windows.net/`** est un service de recherche de bac à sable tenu à jour par l’équipe de développement Recherche Azure. 
+ **`indexes/nycjobs/`** est l’index NYC Jobs dans la collection d’index de ce service. Le nom du service et l’index sont tous deux obligatoires dans la requête.
+ **`docs`** est la collection de documents contenant tout le contenu disponible pour la recherche. La clé d’API de requête fournie dans l’en-tête de requête fonctionne uniquement sur les opérations de lecture ciblant la collection de documents.
+ **`api-version=2017-11-11`** définit la version de l’API, qui est un paramètre requis dans chaque requête.
+ **`search=*`** est la chaîne de requête qui, dans la requête initiale, est nulle, ce qui retourne les 50 premiers résultats (par défaut).

## <a name="send-your-first-query"></a>Envoyer votre première requête

En guise d’étape de vérification, collez la requête suivante dans GET et cliquez sur **Envoyer**. Les résultats sont retournés sous forme de documents JSON détaillés. Vous pouvez copier-coller cette URL dans le premier exemple ci-dessous.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

La chaîne de requête **`search=*`**, est une recherche non spécifiée équivalente à une recherche nulle ou vide. Elle n’est pas particulièrement utile, mais c’est la recherche la plus simple que vous puissiez effectuer.

Si vous le souhaitez, vous pouvez ajouter **`$count=true`** à l’URL pour retourner le nombre de documents correspondant aux critères de recherche. Dans une chaîne de recherche vide, il s’agit de tous les documents dans l’index (2802 dans le cas de NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Appel de l’analyse Lucene complète

Ajoutez **queryType=full** pour appeler la syntaxe de requête complète et substituer la syntaxe de requête simple par défaut. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&search=*
```

Tous les exemples de cet article spécifient le paramètre de requête **queryType=full**, ce qui indique que la syntaxe complète est traitée par l’analyseur de requêtes Lucene. 

## <a name="example-1-field-scoped-query"></a>Exemple 1 : Requête sur un champ

La première requête n’est pas une démonstration de la syntaxe Lucene complète (elle fonctionne à la fois pour la syntaxe simple et complète), mais nous commençons par cet exemple afin d’introduire un concept de requête de base de référence qui génère une réponse JSON raisonnablement lisible. Par souci de concision, la requête cible uniquement le champ *business_title* et spécifie que seuls les titres de fonctions sont retournés. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=*
```

Le paramètre **searchFields** restreint la recherche au champ de titre de fonction. Le paramètre **select** détermine les champs qui sont inclus dans le jeu de résultats.

La réponse pour cette requête doit ressembler à la capture d’écran suivante.

  ![Exemple de réponse Postman](media/search-query-lucene-examples/postman-sample-results.png)

Vous avez peut-être remarqué que le score de recherche était également retourné pour chaque document, bien qu’il ne soit pas spécifié. Cela est dû au fait que le score de recherche est une métadonnée, la valeur indiquant le classement des résultats. Des scores uniformes de 1 se produisent en l’absence de classement, soit car la recherche n’était pas une recherche en texte intégral, soit car il n’y a aucun critère à appliquer. Pour la recherche de valeur null, il n’existe aucun critère et les lignes sont retournées dans un ordre arbitraire. À mesure que les critères de recherche assumeront davantage de définition, vous constaterez que les scores deviendront des valeurs significatives.

## <a name="example-2-in-field-filtering"></a>Exemple 2 : Filtrage dans un champ

La syntaxe Lucene complète prend en charge les expressions dans un champ. Cette requête recherche les titres de fonctions contenant le terme « senior » mais pas « junior » :

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:senior+NOT+junior
```

En spécifiant une construction **fieldname:searchterm**, vous pouvez définir une opération de requête portant sur un champ, où le champ est un mot unique et le terme de recherche est également un mot ou une expression unique, éventuellement avec des opérateurs booléens. Voici quelques exemples :

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

Veillez à placer les chaînes entre guillemets si vous souhaitez que les deux chaînes soient évaluées comme une seule entité, comme ici où deux villes distinctes sont recherchées dans le champ d’emplacement. Vérifiez également que l’opérateur est en majuscules, comme c’est le cas ici avec NOT et AND.

Le champ spécifié dans **fieldname:searchterm** doit être un champ pouvant faire l’objet d’une recherche. Pour plus d’informations sur l’utilisation des attributs d’index dans les définitions de champs, consultez [Créer un index (API REST du service Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index) .

## <a name="example-3-fuzzy-search"></a>Exemple 3 : Recherche approximative

La syntaxe Lucene complète prend également en charge la recherche approximative, avec une mise en correspondance des termes qui ont une construction similaire. Pour effectuer une recherche approximative, ajoutez le signe tilde `~` à la fin d’un mot avec un paramètre facultatif, une valeur comprise entre 0 et 2, qui spécifie la distance de modification. Par exemple, `blue~` ou `blue~1` retournent blue, blues et glue.

Cette requête recherche les postes à pourvoir contenant le terme « associate » (délibérément mal orthographié) :

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:asosiate~
```

D’après la [documentation Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), les recherches approximatives sont basées sur la [Distance Levenshtein-Damerau](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

> [!Note]
> Les requêtes approximatives ne sont pas [analysées](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Les types de requête avec des termes incomplets (requête de préfixe, par caractères génériques, d’expression régulière, approximative) sont ajoutés directement à l’arborescence de requête, en ignorant la phase d’analyse. La seule transformation effectuée sur les termes de requête incomplets est l’utilisation de minuscules.
>

## <a name="example-4-proximity-search"></a>Exemple 4 : Recherche de proximité
Les recherches de proximité servent à rechercher des termes qui sont proches les uns des autres dans un document. Insérez un signe tilde « ~ » à la fin d’une expression, suivi du nombre de mots qui créent la limite de proximité. Par exemple, "hotel airport"~5 recherche les termes hotel et airport distants de cinq mots ou moins dans un document.

Dans cette requête, on recherche les postes contenant le terme « senior analyst » où les deux mots sont séparés au plus par un mot :

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~1
```

Réessayez, mais en supprimant les mots entre « senior analyst ». Notez que huit documents sont retournés pour cette requête, par opposition à 10 pour la requête précédente.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exemple 5 : Promotion de termes
La promotion de termes signifie que vous pouvez accorder à un document un rang plus élevé s’il contient le terme promu, par rapport aux documents qui ne contiennent pas ce terme. Pour promouvoir un terme, utilisez le signe « ^ » avec un facteur de promotion (un nombre) à la fin du terme recherché. 

Dans cette requête « avant », on recherche les postes à pourvoir contenant le terme *computer analyst* et on constate l’absence de résultat avec les deux mots *computer* et *analyst*. Par contre, les postes *computer* figurent en haut des résultats.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst
```

Dans la requête « après », on répète la recherche, cette fois en promouvant les résultats contenant le terme *analyst* par rapport au terme *computer* si les deux mots ensemble n’existent pas. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst%5e2
```
Une version plus lisible de la requête ci-dessus est `search=business_title:computer analyst^2`. Pour obtenir une requête exploitable, `^2` est encodé sous la forme `%5E2`, ce qui est plus difficile à voir.

Il ne faut pas confondre la promotion de termes avec les profils de score, qui promeuvent certains champs plutôt que des termes spécifiques. L’exemple suivant permet d’illustrer les différences entre les deux.

Prenez un profil de score qui promeut les correspondances figurant dans un certain champ, par exemple **genre** dans l’exemple musicstoreindex. En utilisant la promotion de termes, vous pouvez promouvoir encore plus certains termes de recherche. Par exemple, "rock^2 electronic" promeut les documents qui contiennent les termes de recherche dans le champ **genre** , par rapport aux autres champs de recherche de l’index. Par ailleurs, les documents qui contiennent le terme de recherche « rock » bénéficient d’un classement plus élevé que ceux qui contiennent le terme de recherche « electronic » en raison de la valeur de promotion du terme (2).

Lors de la définition du niveau de facteur, plus le facteur de promotion est élevé, plus le terme est pertinent par rapport aux autres termes de recherche. Par défaut, le facteur de promotion est égal à 1. Ce facteur doit être positif, mais il peut être inférieur à 1 (par exemple 0,2).


## <a name="example-6-regex"></a>Exemple 6 : Regex

Une recherche d’expression régulière trouve une correspondance en fonction du contenu placé entre des barres obliques « / », comme le décrit la [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

Dans cette requête, on recherche les tâches contenant le terme Senior ou junior : `search=business_title:/(Sen|Jun)ior/``.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:/(Sen|Jun)ior/
```
> [!Note]
> Les requêtes Regex ne sont pas [analysées](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). La seule transformation effectuée sur les termes de requête incomplets est l’utilisation de minuscules.
>

## <a name="example-7-wildcard-search"></a>Exemple 7 : Recherche par caractères génériques
Vous pouvez utiliser la syntaxe généralement reconnue pour effectuer des recherches avec plusieurs caractères génériques (\*) ou un caractère générique unique (?). Notez que l’Analyseur de requêtes Lucene prend en charge l’utilisation de ces symboles avec un terme unique, et non une expression.

Dans cette requête, on recherche les postes à pourvoir qui contiennent le préfixe « prog », par exemple ceux contenant les termes « programmation » et « programmeur ».

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:prog*
```
Vous ne pouvez pas utiliser un signe * ou ? comme premier caractère d’une recherche.

> [!Note]
> Les requêtes par caractères génériques ne sont pas [analysées](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). La seule transformation effectuée sur les termes de requête incomplets est l’utilisation de minuscules.
>

## <a name="next-steps"></a>Étapes suivantes
Essayez de spécifier l’Analyseur de requêtes Lucene dans votre code. Les liens suivants expliquent comment configurer des requêtes de recherche pour .NET et l’API REST. Ces liens utilisent la syntaxe simple par défaut. Vous devrez donc appliquer ce que vous avez appris dans cet article pour spécifier le **queryType**.

* [Interroger un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-query-dotnet.md)
* [Interroger votre index Azure Search à l’aide de l’API REST](search-query-rest-api.md)

Vous trouverez des informations de référence supplémentaires sur la syntaxe et sur l’architecture de requête, ainsi que des exemples, en cliquant sur les liens suivants :

+ [Exemples de requête de syntaxe simple](search-query-simple-examples.md)
+ [Fonctionnement de la recherche en texte intégral dans la Recherche Azure](search-lucene-query-architecture.md)
+ [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe de requête complète Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)