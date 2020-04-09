---
title: Créer une requête simple
titleSuffix: Azure Cognitive Search
description: Apprenez, à l’aide d’exemples, à exécuter des requêtes basées sur la syntaxe simple pour effectuer des recherches en texte intégral, filtrées, géographiques ou à facettes sur un index de Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 42ace2444c7d8e790c7575a7c1a7b3b1219d090a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656422"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Créer une requête simple dans la Recherche cognitive Azure

Dans la Recherche cognitive Azure, la [syntaxe de requête simple](query-simple-syntax.md) appelle l’analyseur de requêtes par défaut pour l’exécution de requêtes de recherche en texte intégral sur un index. Cet analyseur rapide gère des scénarios courants, notamment la recherche en texte intégral, filtrée et à facettes ainsi que la recherche géographique. 

Dans cet article, nous utilisons des exemples pour illustrer la syntaxe simple.

L’autre syntaxe de requête disponible est la syntaxe [Lucene complète](query-lucene-syntax.md), qui prend en charge des structures de requête plus complexes comme la recherche approximative et par caractères génériques, dont le traitement peut être plus long. Pour plus d’informations et pour obtenir des exemples illustrant la syntaxe complète, consultez l’article sur l’[utilisation de la syntaxe Lucene complète](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formuler des requêtes dans Postman

Les exemples suivants utilisent un index de recherche NYC Jobs composé de postes à pourvoir sur la base d’un jeu de données fourni par l’initiative [City of New York OpenData](https://nycopendata.socrata.com/). Ces données ne doivent pas être considérées comme étant à jour ou complètes. L’index se trouve sur un service de bac à sable fourni par Microsoft, ce qui signifie que vous n’avez pas besoin d’abonnement Azure ni de Recherche cognitive Azure pour essayer ces requêtes.

En revanche, vous avez besoin de Postman ou d’un outil équivalent pour émettre la requête HTTP sur GET. Pour plus d’informations, consultez [Démarrage rapide : Explorer les API REST de Recherche cognitive Azure avec Postman](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Définir l’en-tête de requête

1. Dans l’en-tête de requête, affectez la valeur `application/json` à **Content-Type**.

2. Ajoutez un **api-key** et affectez-lui cette chaîne : `252044BE3886FE4A8E3BAA4F595114BB`. Il s’agit d’une clé de requête pour le service de recherche de bac à sable qui héberge l’index NYC Jobs.

Une fois que vous avez spécifié l’en-tête de requête, vous pouvez le réutiliser pour toutes les requêtes dans cet article, en remplaçant uniquement la chaîne **search=** . 

  ![En-tête de demande Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Définir l’URL de requête

La requête est une commande GET accompagnée d’une URL contenant le point de terminaison de Recherche cognitive Azure et la chaîne de recherche.

  ![En-tête de demande Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

L’URL est composée des éléments suivants :

+ **`https://azs-playground.search.windows.net/`** est un service de recherche de bac à sable tenu à jour par l’équipe de développement de Recherche cognitive Azure. 
+ **`indexes/nycjobs/`** est l’index NYC Jobs dans la collection d’index de ce service. Le nom du service et l’index sont tous deux obligatoires dans la requête.
+ **`docs`** est la collection de documents contenant tout le contenu disponible pour la recherche. La clé d’API de requête fournie dans l’en-tête de requête fonctionne uniquement sur les opérations de lecture ciblant la collection de documents.
+ **`api-version=2019-05-06`** définit la version de l’API, qui est un paramètre requis dans chaque requête.
+ **`search=*`** est la chaîne de requête qui, dans la requête initiale, est nulle, ce qui retourne les 50 premiers résultats (par défaut).

## <a name="send-your-first-query"></a>Envoyer votre première requête

En guise d’étape de vérification, collez la requête suivante dans GET et cliquez sur **Envoyer**. Les résultats sont retournés sous forme de documents JSON détaillés. Des documents entiers sont retournés, ce qui vous permet de voir tous les champs et toutes les valeurs.

Collez cette URL dans un client REST comme étape de validation et pour afficher la structure du document.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

La chaîne de requête **`search=*`** , est une recherche non spécifiée équivalente à une recherche nulle ou vide. Elle n’est pas particulièrement utile, mais c’est la recherche la plus simple que vous puissiez effectuer.

Si vous le souhaitez, vous pouvez ajouter **`$count=true`** à l’URL pour retourner le nombre de documents correspondant aux critères de recherche. Une chaîne de recherche vide correspond à tous les documents figurant dans l’index (environ 2800 dans le cas de NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Appel de l’analyse de requête simple

Pour les requêtes interactives, vous n’avez rien à spécifier : l’analyse simple est l’analyse par défaut. Dans le code, si vous avez déjà appelé **queryType=full** pour la syntaxe de requête complète, vous pouvez rétablir la valeur par défaut avec **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Exemple 1 : Requête sur des champs

Ce premier exemple n’est pas propre à un analyseur, mais nous permet d’introduire le premier concept de requête fondamental : la contenance. Cet exemple limite l’exécution de la requête et la réponse à quelques champs spécifiques. Lors de l’utilisation de l’outil Postman ou Explorateur de recherche, il est important de connaître la structure d’une réponse JSON accessible en lecture. 

Par souci de concision, la requête cible uniquement le champ *business_title* et spécifie que seuls les titres de fonctions sont retournés. La syntaxe est **searchFields** pour restreindre l’exécution de la requête au champ business_title, ainsi que **select** pour spécifier les champs inclus dans la réponse.

### <a name="partial-query-string"></a>Chaîne de requête partielle

```http
searchFields=business_title&$select=business_title&search=*
```

Voici la même requête avec plusieurs champs dans une liste de valeurs séparées par des virgules.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>URL complète

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

La réponse pour cette requête doit ressembler à la capture d’écran suivante.

  ![Exemple de réponse Postman](media/search-query-lucene-examples/postman-sample-results.png)

Vous avez peut-être remarqué le score de recherche dans la réponse. Des scores uniformes de 1 sont obtenus en l’absence de classement, soit parce que la recherche n’était pas une recherche en texte intégral, soit parce qu’aucun critère n’a été appliqué. Pour la recherche de valeur Null sans aucun critère, les lignes sont renvoyées dans un ordre arbitraire. Si vous incluez des critères réels, vous constaterez que les scores de recherche deviendront des valeurs significatives.

## <a name="example-2-look-up-by-id"></a>Exemple 2 : Recherche par ID

Cet exemple est un peu atypique, mais lors de l’évaluation des comportements de recherche, vous souhaiterez peut-être inspecter l’ensemble du contenu d’un document spécifique afin de comprendre pourquoi il a été inclus dans les résultats ou exclu de ces derniers. Pour obtenir un document unique dans sa totalité, utilisez une [opération de recherche](https://docs.microsoft.com/rest/api/searchservice/lookup-document) afin de transmettre l’ID du document.

Tous les documents ont un identificateur unique. Pour tester la syntaxe d’une requête de recherche, retournez tout d’abord une liste d’ID de document afin d’en trouver un à utiliser. Pour NYC Jobs, les identificateurs sont stockés dans le champ `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

L’exemple suivant est une requête de recherche qui retourne un document spécifique basé sur `id` « 9E1E3AF9-0660-4E00-AF51-9B654925A2D5 », qui est apparu en premier dans la réponse précédente. La requête suivante retourne le document entier, et pas seulement certains champs. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exemple 3 : Requêtes de filtre

La [syntaxe de filtre](https://docs.microsoft.com/azure/search/search-query-odata-filter) est une expression OData que vous pouvez utiliser avec une **recherche** ou de façon autonome. Un filtre autonome, dépourvu de paramètre de recherche, est utile quand l’expression de filtre est en mesure de qualifier complètement les documents d’intérêt. En l’absence d’une chaîne de requête, il n’y a ni analyse lexicale ou linguistique, ni scoring (tous les scores sont égaux à 1), ni classement. Vous pouvez remarquer que la chaîne de recherche est vide.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Utilisés conjointement, le filtre est d’abord appliqué à la totalité de l’index et la recherche est effectuée sur les résultats du filtre. Les filtres peuvent donc être utiles pour améliorer les performances des requêtes, puisqu’ils limitent le nombre de documents que devra traiter la requête de recherche.

  ![Réponse d’une requête de filtre](media/search-query-simple-examples/filtered-query.png)

Si vous souhaitez tester ce type de requête dans Postman à l’aide de GET, vous pouvez coller la chaîne suivante :

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Une autre méthode efficace pour combiner un filtre et une recherche consiste à utiliser **`search.ismatch*()`** dans une expression de filtre, dans laquelle vous pouvez utiliser une requête de recherche dans le filtre. Cette expression de filtre utilise un caractère générique sur *plan* afin de sélectionner les documents dont le champ business_title contient les termes plan, planificateur, planification, et ainsi de suite.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Pour plus d’informations sur la fonction, consultez la [description de la fonction search.ismatch dans les exemples de filtre](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples).

## <a name="example-4-range-filters"></a>Exemple 4 : Filtres de plage

Le filtrage de plage est pris en charge par le biais des expressions **`$filter`** pour n’importe quel type de données. Les exemples ci-après effectuent des recherches sur des champs numériques et de chaîne. 

Les types de données sont importants dans les filtres de plage et fonctionnent mieux lorsque les données numériques se trouvent dans des champs numériques, et les données de chaîne dans des champs de chaîne. L’utilisation de données numériques dans les champs de chaîne n’est pas adaptée aux plages, car les chaînes numériques ne sont pas comparables dans la Recherche cognitive Azure. 

Les exemples ci-après sont fournis au format POST à des fins de lisibilité (plage numérique, suivie d’une plage de texte) :

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filtre de plage pour les plages numériques](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtre de plage pour les plages de texte](media/search-query-simple-examples/rangefiltertext.png)

Vous pouvez également tester ces filtres dans Postman à l’aide de GET :

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> L’utilisation de facettes sur des plages de valeurs est une condition d’application de recherche courante. Pour plus d’informations et d’exemples sur la génération de filtres pour les structures de navigation à facettes, consultez la section [« Filtrer sur une plage de valeurs » de l’article *Implémentation de la navigation à facettes*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exemple 5 : Recherche basée sur la localisation

L’exemple d’index inclut un champ geo_location avec des coordonnées de latitude et de longitude. Cet exemple utilise la [fonction geo.distance](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) qui applique un filtre sur les documents situés à une distance arbitraire (en kilomètres) d’un point de départ que vous spécifiez. Vous pouvez ajuster la dernière valeur de la requête (4) pour réduire ou étendre la surface de la requête.

L’exemple ci-après est fourni au format POST à des fins de lisibilité :

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Pour améliorer la lisibilité des résultats de la recherche, ces derniers sont tronqués afin d’inclure un ID de travail, un poste et le lieu de travail. Les coordonnées de départ ont été obtenues à partir d’un document aléatoire dans l’index (dans le cas présent, pour le lieu de travail Staten Island).

Vous pouvez également tester cette recherche dans Postman à l’aide de GET :

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exemple 6 : Précision de la recherche

Les requêtes de termes portent sur des termes uniques, pouvant être nombreux, qui sont évalués de manière indépendante. Les requêtes d’expressions sont placées entre guillemets et évaluées comme une chaîne textuelle. La précision de la correspondance est contrôlée par des opérateurs et searchMode.

Exemple 1 : **`&search=fire`** retourne 150 résultats, où toutes les correspondances contiennent le mot fire quelque part dans le document.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Exemple 2 : **`&search=fire department`** retourne 2002 résultats. Des correspondances sont retournées pour les documents contenant fire ou department.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Exemple 3 : **`&search="fire department"`** retourne 82 résultats. La chaîne étant entre guillemets, il s’agit d’une recherche textuelle sur les deux termes, et des correspondances sont trouvées sur les termes tokenisés dans l’index constitués des termes combinés. Cela explique pourquoi une recherche comme **`search=+fire +department`** n’est pas équivalente. Les deux termes sont obligatoires, mais sont analysés de manière indépendante. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exemple 7 : Opérateurs booléens avec searchMode

La syntaxe simple prend en charge les opérateurs booléens sous la forme de caractères (`+, -, |`). Le paramètre searchMode indique le compromis à faire entre précision et rappel, `searchMode=any` favorisant le rappel (la mise en correspondance sur n’importe quel critère qualifie un document pour le jeu de résultats), et `searchMode=all` favorisant la précision (tous les critères doivent être mis en correspondance). La valeur par défaut est `searchMode=any`, ce qui peut être déroutant si vous placez plusieurs opérateurs dans une requête et que vous obtenez des résultats moins affinés que prévu. Cela est particulièrement vrai avec NOT, où les résultats incluent tous les documents « ne contenant pas » un terme spécifique.

Avec le searchMode par défaut (any), 2800 documents sont retournés : ceux contenant le terme en plusieurs parties « fire department », plus tous les documents ne contenant pas le terme « Metrotech Center ».

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![Mode de recherche any](media/search-query-simple-examples/searchmodeany.png)

Affecter la valeur `all` à searchMode applique un effet cumulé aux critères et retourne un jeu de résultats plus petit (21 documents) constitué de documents contenant l’expression entière « fire department », moins les emplois à l’adresse de Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![Mode de recherche all](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Exemple 8 : Structuration des résultats

Plusieurs paramètres contrôlent quels champs figurent dans les résultats de la recherche, le nombre de documents retournés dans chaque lot, ainsi que l’ordre de tri. Cet exemple reprend quelques-uns des exemples précédents, mais limite les résultats à des champs spécifiques à l’aide de l’instruction **$select** et de critères de recherche textuelle, retournant 82 correspondances. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
En ajoutant à l’exemple précédent, vous pouvez trier par titre. Ce tri fonctionne car civil_service_title est *triable* dans l’index.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

La pagination des résultats est implémentée à l’aide du paramètre **$top**, retournant ici les cinq premiers documents :

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Pour obtenir les cinq suivants, ignorez le premier lot :

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Étapes suivantes
Essayez de spécifier des requêtes dans votre code. Les liens suivants expliquent comment configurer des requêtes de recherche pour .NET et l’API REST à l’aide de la syntaxe simple par défaut.

* [Interroger un index à l’aide du SDK .NET](search-query-dotnet.md)
* [Interroger un index à l’aide de l’API REST](search-create-index-rest-api.md)

Vous trouverez des informations de référence supplémentaires sur la syntaxe et sur l’architecture de requête, ainsi que des exemples, en cliquant sur les liens suivants :

+ [Exemples de syntaxe de requête Lucene pour créer des requêtes avancées](search-query-lucene-examples.md)
+ [Fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md)
+ [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Requête complète Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Syntaxe de filtre (Filter) et de tri (Orderby)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
