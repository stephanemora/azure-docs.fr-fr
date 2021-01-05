---
title: Utiliser la syntaxe de requête Lucene complète
titleSuffix: Azure Cognitive Search
description: Syntaxe de requête Lucene pour la recherche approximative, la recherche de proximité, l’amélioration de termes, la recherche d’expressions régulières et la recherche par caractères génériques dans le service Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401455"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Utiliser la syntaxe de recherche Lucene « complète » (requêtes avancées dans Recherche cognitive Azure)

Lors de la construction de requêtes pour Recherche cognitive Azure, vous pouvez remplacer l’[analyseur de requêtes simple](query-simple-syntax.md) par défaut par l’[analyseur de requêtes Lucene dans Recherche cognitive Azure](query-lucene-syntax.md), plus puissant, afin de formuler des définitions de requêtes spécialisées et avancées. 

L’analyseur Lucene prend en charge des constructions de requêtes complexes, telles que des requêtes portant sur des champs, la recherche approximative, la recherche par caractères génériques infixes et suffixes, la recherche de proximité, la promotion de termes et la recherche d’expression régulière. Cette fonctionnalité plus puissante nécessite des capacités de traitement supplémentaires et peut donc entraîner des temps d’exécution un peu plus longs. Dans cet article, vous pouvez parcourir des exemples décrivant les opérations de requête basées sur la syntaxe complète.

> [!Note]
> Une grande partie des constructions de requêtes spécialisées activées par le biais de la syntaxe de requête Lucene complète ne sont pas soumises à une [analyse du texte](search-lucene-query-architecture.md#stage-2-lexical-analysis), ce qui peut être surprenant si vous prévoyez une recherche de radical ou une lemmatisation. L’analyse lexicale est effectuée uniquement sur des termes complets (requête sur un terme ou une expression). Les types de requête avec des termes incomplets (requête de préfixe, de caractère générique, d’expression régulière, partielle) sont ajoutés directement à l’arborescence de requête, en ignorant la phase d’analyse. La seule transformation effectuée sur les termes de requête partiels est l’utilisation de minuscules. 
>

## <a name="nyc-jobs-examples"></a>Exemples NYC Jobs

Les exemples suivants utilisent un [index de recherche NYC Jobs](https://azjobsdemo.azurewebsites.net/) composé de postes à pourvoir sur la base d’un jeu de données fourni par l’[initiative City of New York OpenData](https://nycopendata.socrata.com/). Ces données ne doivent pas être considérées comme étant à jour ou complètes. L’index se trouve sur un service de bac à sable fourni par Microsoft, ce qui signifie que vous n’avez pas besoin d’abonnement Azure ni de Recherche cognitive Azure pour essayer ces requêtes.

En revanche, vous avez besoin de Postman ou d’un outil équivalent pour émettre la requête HTTP sur GET ou POST. Si vous ne connaissez pas bien ces outils, consultez [Démarrage rapide : Explorer l’API REST de Recherche cognitive Azure](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Configurer la requête

1. Les en-têtes de requête doivent comporter les valeurs suivantes :

   | Clé | Valeur |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (Il s’agit de la clé d’API de requête réelle pour le service de recherche de bac à sable qui héberge l’index NYC Jobs) |

1. Définissez le verbe sur **`GET`** .

1. Définissez l’URL sur **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + La collection de documents dans l’index contient tout le contenu disponible pour la recherche. Une clé d’API de requête fournie dans l’en-tête de requête fonctionne uniquement pour les opérations de lecture ciblant la collection de documents.

   + **`$count=true`** retourne le nombre de documents correspondant aux critères de recherche. Une chaîne de recherche vide comptabilisera tous les documents figurant dans l’index (environ 2558 dans le cas de NYC Jobs).

   + **`search=*`** est une requête non spécifiée équivalente à une recherche nulle ou vide. Elle n’est pas particulièrement utile, mais c’est la recherche la plus simple que vous puissiez effectuer, et elle affiche tous les champs récupérables dans l’index, avec toutes les valeurs.

   + **`queryType=full`** appelle l’analyseur Lucene complet.

1. En guise d’étape de vérification, collez la requête suivante dans GET et cliquez sur **Envoyer**. Les résultats sont retournés sous forme de documents JSON détaillés.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Appel de l’analyse Lucene complète

Ajoutez **`queryType=full`** pour appeler la syntaxe de requête complète et substituer la syntaxe de requête simple par défaut. Tous les exemples de cet article spécifient le paramètre de requête **`queryType=full`** , ce qui indique que la syntaxe complète est traitée par l’analyseur de requêtes Lucene. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemple 1 : Requête portée sur une liste de champs

Ce premier exemple n’est pas propre à un analyseur, mais nous permet d’introduire le premier concept de requête fondamental : la contenance. Cet exemple limite l’exécution de la requête et la réponse à quelques champs spécifiques. Lors de l’utilisation de l’outil Postman ou Explorateur de recherche, il est important de connaître la structure d’une réponse JSON accessible en lecture. 

Cette requête cible uniquement *business_title* dans **`searchFields`** , en spécifiant grâce au paramètre **`select`** le même champ dans la réponse.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La réponse pour cette requête doit ressembler à la capture d’écran suivante.

  ![Exemple de réponse Postman avec scores](media/search-query-lucene-examples/postman-sample-results.png)

Vous avez peut-être remarqué le score de recherche dans la réponse. Des scores uniformes de **1** sont obtenus en l’absence de classement, soit parce que la recherche n’était pas une recherche en texte intégral, soit parce qu’aucun critère n’a été fourni. Pour une recherche vide, les lignes reviennent dans un ordre arbitraire. Si vous incluez des critères réels, vous constaterez que les scores de recherche deviendront des valeurs significatives.

## <a name="example-2-fielded-search"></a>Exemple 2 : Recherche par champ

La syntaxe Lucene complète permet de restreindre des expressions de recherche individuelles à un champ spécifique. Cet exemple recherche les titres de fonctions contenant le terme « senior » mais pas « junior ». Vous pouvez spécifier plusieurs champs à l’aide de l’opérateur AND.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

La réponse pour cette requête doit ressembler à la capture d’écran suivante (posting_type n’est pas affiché).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Expression de recherche de réponse de l’exemple Postman" border="false":::

L’expression de recherche peut être un mot ou une phrase, ou une expression plus complexe entre parenthèses, éventuellement avec des opérateurs booléens. Voici quelques exemples :

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Veillez à placer les chaînes entre guillemets si vous souhaitez que les deux chaînes soient évaluées comme une seule entité, comme ici où deux emplacements distincts sont recherchés dans le champ `state`. Selon l’outil, vous devrez peut-être placer les guillemets dans une séquence d’échappement (`\`). 

Le champ spécifié dans **fieldName:searchExpression** doit être un champ pouvant faire l’objet d’une recherche. Pour plus d’informations sur l’utilisation des attributs d’index dans les définitions de champs, consultez [Créer un index (API REST Recherche cognitive Azure)](/rest/api/searchservice/create-index).

> [!NOTE]
> Dans l’exemple ci-dessus, le paramètre **`searchFields`** est omis car chaque partie de la requête comporte un nom de champ explicitement spécifié. Toutefois, vous pouvez toujours utiliser **`searchFields`** si la requête comporte plusieurs parties (à l’aide des instructions AND). Par exemple, la requête `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` ne correspondrait à `senior NOT junior` qu'au niveau du champ `business_title`, alors qu'elle correspondrait au champ « externe » avec le champ `posting_type`. Le nom du champ fourni dans `fieldName:searchExpression` a toujours priorité sur le paramètre **`searchFields`** , c’est pourquoi dans cet exemple, nous pouvons omettre `business_title` dans **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Exemple 3 : Recherche partielle

La syntaxe Lucene complète prend également en charge la recherche approximative, avec une mise en correspondance des termes qui ont une construction similaire. Pour effectuer une recherche partielle, ajoutez le signe tilde `~` à la fin d’un mot avec un paramètre facultatif, une valeur comprise entre 0 et 2, qui spécifie la distance de modification. Par exemple, `blue~` ou `blue~1` retournent blue, blues et glue.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Les phrases ne sont pas prises en charge directement, mais vous pouvez spécifier une correspondance partielle pour chaque terme d’une phrase à plusieurs éléments, par exemple `search=business_title:asosiate~ AND comm~`.  Dans la capture d’écran ci-dessous, la réponse inclut une correspondance pour *community associate*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Réponse de recherche partielle" border="false":::

> [!Note]
> Les requêtes approximatives ne sont pas [analysées](search-lucene-query-architecture.md#stage-2-lexical-analysis). Les types de requête avec des termes incomplets (requête de préfixe, de caractère générique, d’expression régulière, partielle) sont ajoutés directement à l’arborescence de requête, en ignorant la phase d’analyse. La seule transformation effectuée sur les termes de requête partiels est l’utilisation de minuscules.
>

## <a name="example-4-proximity-search"></a>Exemple 4 : Recherche de proximité

Les recherches de proximité servent à rechercher des termes qui sont proches les uns des autres dans un document. Insérez un signe tilde « ~ » à la fin d’une expression, suivi du nombre de mots qui créent la limite de proximité. Par exemple, "hotel airport"~5 recherche les termes hotel et airport distants de cinq mots ou moins dans un document.

Cette requête recherche les termes « senior » et « analyst », où chaque terme est séparé par un seul mot, et les guillemets sont placés dans une séquence d’échappement (`\"`) pour conserver l’expression :

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La réponse pour cette requête doit ressembler à la capture d’écran suivante 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Requête de proximité" border="false":::

Réessayez en éliminant la distance (`~0`) entre les termes « analyste senior ». Notez que huit documents sont retournés pour cette requête, par opposition à 10 pour la requête précédente.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Exemple 5 : Promotion de termes

La promotion de termes signifie que vous pouvez accorder à un document un rang plus élevé s’il contient le terme promu, par rapport aux documents qui ne contiennent pas ce terme. Pour promouvoir un terme, utilisez le signe `^` (caret) avec un facteur de promotion (un nombre) à la fin du terme recherché.

Dans cette requête « avant », on recherche les postes à pourvoir contenant le terme *computer analyst* et on constate l’absence de résultat avec les deux mots *computer* et *analyst*. Par contre, les postes *computer* figurent en haut des résultats.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Dans la requête « après », on répète la recherche, cette fois en promouvant les résultats contenant le terme *analyst* par rapport au terme *computer* si les deux mots ensemble n’existent pas. `search=business_title:computer analyst^2` est une version lisible de la requête. Pour une requête utilisable dans Postman, `^2` est encodé sous la forme `%5E2`.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La réponse pour cette requête doit ressembler à la capture d’écran suivante.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Promotion de termes après" border="false":::

Il ne faut pas confondre la promotion de termes avec les profils de score, qui promeuvent certains champs plutôt que des termes spécifiques. L’exemple suivant permet d’illustrer les différences entre les deux.

Prenez un profil de score qui promeut les correspondances figurant dans un certain champ, par exemple **genre** dans l’exemple musicstoreindex. En utilisant la promotion de termes, vous pouvez promouvoir encore plus certains termes de recherche. Par exemple, "rock^2 electronic" promeut les documents qui contiennent les termes de recherche dans le champ **genre** , par rapport aux autres champs de recherche de l’index. Par ailleurs, les documents qui contiennent le terme de recherche « rock » bénéficient d’un classement plus élevé que ceux qui contiennent le terme de recherche « electronic » en raison de la valeur de promotion du terme (2).

Lors de la définition du niveau de facteur, plus le facteur de promotion est élevé, plus le terme est pertinent par rapport aux autres termes de recherche. Par défaut, le facteur de promotion est égal à 1. Ce facteur doit être positif, mais il peut être inférieur à 1 (par exemple 0,2).

## <a name="example-6-regex"></a>Exemple 6 : Expression régulière

Une recherche d’expression régulière trouve une correspondance en fonction du contenu placé entre des barres obliques « / », comme le décrit la [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La réponse pour cette requête doit ressembler à la capture d’écran suivante.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Requête par expression régulière" border="false":::

> [!Note]
> Les requêtes Regex ne sont pas [analysées](./search-lucene-query-architecture.md#stage-2-lexical-analysis). La seule transformation effectuée sur les termes de requête partiels est l’utilisation de minuscules.
>

## <a name="example-7-wildcard-search"></a>Exemple 7 : Recherche par caractères génériques

Vous pouvez utiliser la syntaxe généralement reconnue pour effectuer des recherches avec plusieurs caractères génériques (\*) ou un caractère générique unique (?). Notez que l’Analyseur de requêtes Lucene prend en charge l’utilisation de ces symboles avec un terme unique, et non une expression.

Dans cette requête, on recherche les postes à pourvoir qui contiennent le préfixe « prog », par exemple ceux contenant les termes « programmation » et « programmeur ». Vous ne pouvez pas utiliser un symbole `*` ou `?` comme premier caractère d’une recherche.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

La réponse pour cette requête doit ressembler à la capture d’écran suivante.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Requête par caractère générique" border="false":::

> [!Note]
> Les requêtes par caractères génériques ne sont pas [analysées](./search-lucene-query-architecture.md#stage-2-lexical-analysis). La seule transformation effectuée sur les termes de requête partiels est l’utilisation de minuscules.
>

## <a name="next-steps"></a>Étapes suivantes

Essayez de spécifier des requêtes dans le code. Les liens suivants expliquent comment configurer des requêtes de recherche à l’aide des Kits de développement logiciel (SDK) Azure.

+ [Interroger un index à l’aide du SDK .NET](search-get-started-dotnet.md)
+ [Interroger un index à l’aide du SDK Python](search-get-started-python.md)
+ [Interroger un index à l’aide du SDK JavaScript](search-get-started-javascript.md)

Vous trouverez des informations de référence supplémentaires sur la syntaxe et sur l’architecture de requête, ainsi que des exemples, en cliquant sur les liens suivants :

+ [Exemples de syntaxe de requête Lucene pour créer des requêtes avancées](search-query-lucene-examples.md)
+ [Fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md)
+ [Syntaxe de requête simple](query-simple-syntax.md)
+ [Syntaxe de requête complète Lucene](query-lucene-syntax.md)
+ [Syntaxe du filtre](search-query-odata-filter.md)