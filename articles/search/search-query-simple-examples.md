---
title: Exemples de requêtes simples pour la Recherche Azure | Microsoft Docs
description: Exemples de requêtes simples pour la recherche en texte intégral, la recherche filtrée, la recherche géographique, la recherche à facettes et autres chaînes de requête utilisées pour interroger un index Recherche Azure.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368597"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Exemples de syntaxe de requête simple pour créer des requêtes dans Recherche Azure

La [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) appelle l’analyseur de requêtes par défaut pour l’exécution de requêtes de recherche en texte intégral par rapport à un index Recherche Azure. L’analyseur de requêtes simples est rapide et gère des scénarios courants dans Recherche Azure, notamment la recherche en texte intégral, la recherche filtrée et à facettes, ainsi que la recherche géographique. Dans cet article, vous allez découvrir des exemples illustrant les opérations de requête disponibles lors de l’utilisation de la syntaxe simple.

L’autre syntaxe de requête disponible est la syntaxe [Lucene complète](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), qui prend en charge des structures de requête plus complexes, comme la recherche approximative et par caractères génériques, dont le traitement peut être plus long. Pour plus d’informations et pour obtenir des exemples illustrant la syntaxe complète, consultez [Exemples de requête de syntaxe Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formuler des requêtes dans Postman

Les exemples suivants utilisent un index de recherche NYC Jobs composé de postes à pourvoir sur la base d’un jeu de données fourni par l’initiative [City of New York OpenData](https://nycopendata.socrata.com/). Ces données ne doivent pas être considérées comme étant à jour ou complètes. L’index se trouve sur un service de bac à sable fourni par Microsoft, ce qui signifie que vous n’avez pas besoin d’abonnement Azure ni de Recherche Azure pour essayer ces requêtes.

En revanche, vous avez besoin de Postman ou d’un outil équivalent pour émettre la requête HTTP sur GET. Pour plus d’informations, consultez [Tester avec les clients REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Définir l’en-tête de requête

1. Dans l’en-tête de requête, affectez la valeur `application/json` à **Content-Type**.

2. Ajoutez un **api-key** et affectez-lui cette chaîne : `252044BE3886FE4A8E3BAA4F595114BB`. Il s’agit d’une clé de requête pour le service de recherche de bac à sable qui héberge l’index NYC Jobs.

Une fois que vous avez spécifié l’en-tête de requête, vous pouvez le réutiliser pour toutes les requêtes dans cet article, en remplaçant uniquement la chaîne **search=**. 

  ![En-tête de demande Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Définir l’URL de requête

La requête est une commande GET accompagnée d’une URL contenant le point de terminaison Recherche Azure et la chaîne de recherche.

  ![En-tête de recherche Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

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

## <a name="how-to-invoke-simple-query-parsing"></a>Appel de l’analyse de requête simple

Pour les requêtes interactives, vous n’avez rien à spécifier : l’analyse simple est l’analyse par défaut. Dans le code, si vous avez déjà appelé **queryType=full** pour la syntaxe de requête complète, vous pouvez revenir à la valeur par défaut avec **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Exemple 1 : Requête sur un champ

La première requête n’est pas propre à la syntaxe (elle fonctionne à la fois pour la syntaxe simple et complète), mais nous commençons par cet exemple afin d’introduire un concept de requête de référence qui génère une réponse JSON raisonnablement lisible. Par souci de concision, la requête cible uniquement le champ *business_title* et spécifie que seuls les titres de fonctions sont retournés. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

Le paramètre **searchFields** restreint la recherche au champ de titre de fonction. Le paramètre **select** détermine les champs qui sont inclus dans le jeu de résultats.

La réponse pour cette requête doit ressembler à la capture d’écran suivante.

  ![Exemple de réponse Postman](media/search-query-lucene-examples/postman-sample-results.png)

Vous avez peut-être remarqué que le score de recherche était également retourné pour chaque document, bien qu’il ne soit pas spécifié. Cela est dû au fait que le score de recherche est une métadonnée, la valeur indiquant le classement des résultats. Des scores uniformes de 1 se produisent en l’absence de classement, soit car la recherche n’était pas une recherche en texte intégral, soit car il n’y a aucun critère à appliquer. Pour la recherche de valeur null, il n’existe aucun critère et les lignes sont retournées dans un ordre arbitraire. À mesure que les critères de recherche assumeront davantage de définition, vous constaterez que les scores deviendront des valeurs significatives.

## <a name="example-2-look-up-by-id"></a>Exemple 2 : Rechercher par ID

Cet exemple est un peu atypique, mais lors de l’évaluation des comportements de recherche, vous souhaiterez peut-être inspecter tout le contenu d’un document afin de comprendre pourquoi il a été inclus ou exclu des résultats. Pour retourner un document entier, utilisez une [opération de recherche](https://docs.microsoft.com/rest/api/searchservice/lookup-document) afin de passer l’ID de document.

Tous les documents ont un identificateur unique. Pour tester la syntaxe d’une requête de recherche, retournez tout d’abord une liste d’ID de document afin d’en trouver un à utiliser. Pour NYC Jobs, les identificateurs sont stockés dans le champ `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

L’exemple suivant est une requête de recherche qui retourne un document spécifique basé sur `id` « 9E1E3AF9-0660-4E00-AF51-9B654925A2D5 », qui est apparu en premier dans la réponse précédente. La requête suivante retourne le document entier, et pas seulement certains champs. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>Exemple 3 : Précision de la recherche

Les requêtes de termes portent sur des termes uniques, pouvant être nombreux, qui sont évalués de manière indépendante. Les requêtes d’expressions sont placées entre guillemets et évaluées comme une chaîne textuelle. La précision de la correspondance est contrôlée par des opérateurs et searchMode.

Exemple 1 : **`&search=fire`** retourne 150 résultats, où toutes les correspondances contiennent le mot fire quelque part dans le document.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Exemple 2 : **`&search=fire department`** retourne 2002 résultats. Des correspondances sont retournées pour les documents contenant fire ou department.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Exemple 3 : **`&search="fire department"`** retourne 82 résultats. La chaîne étant entre guillemets, il s’agit d’une recherche textuelle sur les deux termes, et des correspondances sont trouvées sur les termes tokenisés dans l’index constitués des termes combinés. Cela explique pourquoi une recherche comme **`search=+fire +department`** n’est pas équivalente. Les deux termes sont obligatoires, mais sont analysés de manière indépendante. 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>Exemple 4 : Opérateurs booléens avec searchMode

La syntaxe simple prend en charge les opérateurs booléens sous la forme de caractères (`+, -, |`). Le paramètre searchMode indique le compromis à faire entre précision et rappel, `searchMode=any` favorisant le rappel (la mise en correspondance sur n’importe quel critère qualifie un document pour le jeu de résultats), et `searchMode=all` favorisant la précision (tous les critères doivent être mis en correspondance). La valeur par défaut est `searchMode=any`, ce qui peut être déroutant si vous placez plusieurs opérateurs dans une requête et que vous obtenez des résultats moins affinés que prévu. Cela est particulièrement vrai avec NOT, où les résultats incluent tous les documents « ne contenant pas » un terme spécifique.

Avec le searchMode par défaut (any), 2800 documents sont retournés : ceux contenant le terme en plusieurs parties « fire department », plus tous les documents ne contenant pas le terme « Metrotech Center ».

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
Affecter la valeur `all` à searchMode applique un effet cumulé aux critères et retourne un jeu de résultats plus petit (21 documents) constitué de documents contenant l’expression entière « fire department », moins les emplois à l’adresse de Metrotech Center.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>Exemple 5 : Structuration des résultats

Plusieurs paramètres contrôlent quels champs figurent dans les résultats de la recherche, le nombre de documents retournés dans chaque lot, ainsi que l’ordre de tri. Cet exemple reprend quelques-uns des exemples précédents, mais limite les résultats à des champs spécifiques à l’aide de l’instruction **$select** et de critères de recherche textuelle, retournant 82 correspondances. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
En ajoutant à l’exemple précédent, vous pouvez trier par titre. Ce tri fonctionne car civil_service_title est *triable* dans l’index.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

La pagination des résultats est implémentée à l’aide du paramètre **$top**, retournant ici les cinq premiers documents :

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Pour obtenir les cinq suivants, ignorez le premier lot :

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Étapes suivantes
Essayez de spécifier des requêtes dans votre code. Les liens suivants expliquent comment configurer des requêtes de recherche pour .NET et l’API REST à l’aide de la syntaxe simple par défaut.

* [Interroger un index Recherche Azure à l’aide du kit SDK .NET](search-query-dotnet.md)
* [Interroger votre index Recherche Azure à l’aide de l’API REST](search-query-rest-api.md)

Vous trouverez des informations de référence supplémentaires sur la syntaxe et sur l’architecture de requête, ainsi que des exemples, en cliquant sur les liens suivants :

+ [Exemples de syntaxe de requête Lucene pour créer des requêtes avancées](search-query-lucene-examples.md)
+ [Fonctionnement de la recherche en texte intégral dans la Recherche Azure](search-lucene-query-architecture.md)
+ [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Requête complète Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
