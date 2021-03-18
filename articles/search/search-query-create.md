---
title: Créer une requête
titleSuffix: Azure Cognitive Search
description: Découvrez la procédure de construction d’une demande de requête dans Recherche cognitive, les outils et API à utiliser pour les tests et le code, ainsi que les décisions de requête, qui commencent par la conception d’index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 5a89e9ae05b0733c865d537ffeb1714d3b3ebef1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489359"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Création de requêtes dans Recherche cognitive Azure

Si vous générez une requête pour la première fois, cet article décrit les approches et les méthodes de configuration des requêtes. Il présente également une demande de requête et explique l’impact que peuvent avoir les attributs de champ et les analyseurs linguistiques sur les résultats de la requête.

## <a name="whats-a-query-request"></a>Qu’est-ce qu’une demande de requête ?

Il s’agit d’une requête en lecture seule sur la collection de documents d’un index de recherche unique. Elle spécifie un paramètre de recherche qui contient l’expression de requête, composée de termes, d’expressions entre guillemets et d’opérateurs.

Des paramètres supplémentaires fournissent davantage de définition à la requête et à la réponse. Par exemple, « searchFields » limite l’exécution des requêtes à des champs spécifiques, « select » spécifie les champs qui sont retournés dans les résultats, et « count » retourne le nombre de correspondances trouvées dans l’index.

L’exemple suivant donne une idée générale d’une demande de requête en indiquant un sous-ensemble des paramètres disponibles. Pour plus d’informations sur la composition des requêtes, consultez [Types de requêtes et compositions](search-query-overview.md) et [Rechercher dans des documents (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>Choisir un client

Vous aurez besoin d’un outil, tel que le portail Azure ou Postman, ou du code qui instancie un client de requête à l’aide d’API. Nous vous recommandons le portail Azure ou les API REST pour les premières étapes de développement et les tests de preuve de concept.

### <a name="permissions"></a>Autorisations

Toute opération, y compris les demandes de requête, fonctionnera sous une [clé API d’administration](search-security-api-keys.md), mais les demandes de requête peuvent éventuellement utiliser une [clé API de requête](search-security-api-keys.md#create-query-keys). Les clés API de requête sont vivement recommandées. Vous pouvez en créer jusqu’à 50 par service et affecter des clés différentes à différentes applications.

Dans le portail Azure, l’accès aux outils, aux assistants et aux objets requiert l’appartenance au rôle Contributeur ou supérieur sur le service. 

### <a name="use-azure-portal-to-query-an-index"></a>Utiliser le portail Azure pour créer un index

Le [Navigateur de recherche (portail)](search-explorer.md) est une interface de requête dans le portail Azure qui exécute des requêtes dans des index sur le service de recherche sous-jacent. En interne, le portail effectue des requêtes [Recherche dans des documents](/rest/api/searchservice/search-documents), mais ne peut pas appeler l’autocomplétion, les suggestions ni la recherche dans des documents. 

Vous pouvez sélectionner n’importe quel index et version de l’API REST, y compris la version préliminaire. Une chaîne de requête peut utiliser une syntaxe simple ou complète, avec prise en charge de tous les paramètres de requête (Filter, Select, searchFields, etc.). Dans le portail, lorsque vous ouvrez un index, vous pouvez utiliser le Navigateur de recherche en même temps que la définition JSON de l’index dans les onglets côte à côte pour faciliter l’accès aux attributs du champ. Vérifiez les champs pouvant faire l’objet d’une recherche, d’un tri, de filtrage et de facettes lors du test des requêtes.

### <a name="use-a-rest-client"></a>Utiliser un client REST

Postman et Visual Studio Code (avec une extension pour Recherche cognitive Azure) peuvent tous deux fonctionner comme client de requête. En utilisant l’un ou l’autre de ces outils, vous pouvez vous connecter à votre service de recherche et envoyer des requêtes [Rechercher dans des documents (REST)](/rest/api/searchservice/search-documents). De nombreux tutoriels et exemples illustrent le fonctionnement des clients REST pour l’interrogation d’index. 

Commencez par l’un de ces articles pour en savoir plus sur chaque client (tous deux comprennent des instructions pour les requêtes) :

+ [Créer un index de recherche à l’aide de REST et de Postman](search-get-started-rest.md)
+ [Bien démarrer avec Visual Studio Code et Recherche cognitive Azure](search-get-started-vs-code.md)

Chaque requête est autonome. vous devez donc fournir le point de terminaison, le nom de l’index et la version de l’API à chaque demande. D’autres propriétés, Content-Type et clé API, sont transmises à l’en-tête de demande. Pour plus d’informations, consultez [Rechercher dans des documents (REST)](/rest/api/searchservice/search-documents) pour obtenir de l’aide sur la formulation des demandes de requête.

### <a name="use-an-sdk"></a>Utiliser un Kit de développement logiciel (SDK)

Pour Recherche cognitive, les Kits de développement logiciel (SDK) Azure implémentent des fonctionnalités généralement disponibles. Ainsi, vous pouvez utiliser n’importe lequel des Kits de développement logiciel (SDK) pour interroger un index. Tous fournissent un **SearchClient** qui offre des méthodes permettant d’interagir avec un index, du chargement d’un index avec des documents de recherche, à la formulation de demandes de requête.

| Azure SDK | Client | Exemples |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [readonlyQuery.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query.py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Choisir un type de requête : simple | complète

Si votre requête est une recherche de texte intégral, un analyseur de requête est utilisé pour traiter tout texte transmis sous la forme de termes de recherche et d’expressions. Recherche cognitive Azure offre deux analyseurs de requêtes. 

+ L’analyseur simple comprend la [syntaxe de requête simple](query-simple-syntax.md). Cet analyseur a été sélectionné comme valeur par défaut pour sa vitesse et son efficacité dans les requêtes de texte de forme libre. La syntaxe prend en charge les opérateurs de recherche courants (AND, OR, NOT) pour les recherches de termes et d’expressions, ainsi que la recherche de préfixe (`*`) (comme dans « sea* » pour Seattle et Seaside). Une recommandation générale consiste à essayer d’abord l’analyseur simple, puis à passer à l’analyseur complet si les exigences de l’application nécessitent des requêtes plus puissantes.

+ La [syntaxe de requête Lucene complète](query-Lucene-syntax.md#bkmk_syntax), activée lorsque vous ajoutez `queryType=full` à la demande, est basée sur l’[analyseur Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Une syntaxe complète et une syntaxe simple se chevauchent dans la mesure où toutes deux prennent en charge le même préfixe et les mêmes opérations booléennes, mais la syntaxe complète fournit davantage d’opérateurs. Dans la syntaxe complète, il existe d’autres opérateurs pour les expressions booléennes, et plus d’opérateurs pour les requêtes avancées, telles que la recherche approximative, la recherche de caractères génériques, la recherche de proximité et les expressions régulières.

## <a name="choose-query-methods"></a>Choisir des méthodes d’interrogation

La recherche est essentiellement un exercice piloté par l’utilisateur, où les termes ou expressions sont collectés à partir d’une zone de recherche ou à partir d’événements de clic sur une page. Le tableau suivant récapitule les mécanismes par lesquels vous pouvez collecter les entrées utilisateur, ainsi que l’expérience de recherche attendue.

| Entrée | Expérience |
|-------|---------|
| [Méthode de recherche](/rest/api/searchservice/search-documents) | Un utilisateur tape des termes ou des expressions dans une zone de recherche, avec ou sans opérateurs, et clique sur Rechercher pour envoyer la demande. La recherche peut être utilisée avec des filtres sur la même demande, mais pas avec la saisie semi-automatique ni les suggestions. |
| [Méthode de saisie semi-automatique](/rest/api/searchservice/autocomplete) | Un utilisateur tape quelques caractères et des requêtes sont lancées après la saisie de chaque nouveau caractère. La réponse est une chaîne complétée à partir de l’index. Si la chaîne fournie est valide, l’utilisateur clique sur Rechercher pour envoyer cette requête au service. |
| [Méthode de suggestions](/rest/api/searchservice/suggestions) | Comme avec la saisie semi-automatique, un utilisateur tape quelques caractères et des requêtes incrémentielles sont générées. La réponse est une liste déroulante de documents correspondants, généralement représentée par quelques champs uniques ou descriptifs. Si l’une des sélections est valide, l’utilisateur clique dessus et le document correspondant est retourné. |
| [Navigation à facettes](/rest/api/searchservice/search-documents#query-parameters) | Une page affiche des liens de navigation cliquables ou des barres de navigation qui réduisent la portée de la recherche. Une structure de navigation à facettes est composée dynamiquement en fonction d’une requête initiale. Par exemple, `search=*` pour remplir une arborescence de navigation à facettes composée de chaque catégorie possible. Une structure de navigation à facettes est créée à partir d’une réponse à une requête, mais il s’agit également d’un mécanisme permettant d’exprimer la requête suivante. Dans la référence d’API REST, `facets` est documenté comme un paramètre de requête d’une opération Recherche dans des documents, mais peut être utilisé sans le paramètre `search`.|
| [Méthode de filtre](/rest/api/searchservice/search-documents#query-parameters) | Les filtres sont utilisés avec des facettes pour affiner les résultats. Vous pouvez également implémenter un filtre derrière la page, par exemple pour initialiser la page avec des champs spécifiques à une langue. Dans la référence d’API REST, `$filter` est documenté comme un paramètre de requête d’une opération Recherche dans des documents, mais peut être utilisé sans le paramètre `search`.|

## <a name="know-your-field-attributes"></a>Découvrir vos attributs de champ

Si vous avez déjà passé en revue les [types de requêtes et la composition](search-query-overview.md), vous vous souvenez peut-être que les paramètres de la demande de requête dépendent de la façon dont les champs sont attribués dans un index. Par exemple, pour être utilisé dans une requête, un filtre ou un ordre de tri, un champ doit pouvoir faire l’objet d’une *recherche*, d’un *filtrage* et d’un *tri*. De même, seuls les champs marqués comme *récupérables* peuvent apparaître dans les résultats. Lorsque vous commencez à spécifier les paramètres `search`, `filter` et `orderby` dans votre demande, veillez à vérifier les attributs à mesure que vous progressez pour éviter des résultats inattendus.

Dans la capture d’écran du portail ci-dessous de l’[exemple d’index Hotels](search-get-started-portal.md), seuls les deux derniers champs « LastRenovationDate » et « Rating » peuvent être utilisés dans une clause `"$orderby"` uniquement.

![Définition d’index pour l’exemple des hôtels](./media/search-query-overview/hotel-sample-index-definition.png "Définition d’index pour l’exemple Hôtel")

Pour obtenir une description des attributs de champ, consultez [Créer un index (API REST)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Découvrir vos jetons

Pendant l’indexation, le moteur de recherche utilise un analyseur pour effectuer une analyse de texte sur les chaînes, ce qui optimise le potentiel de correspondance au moment de la requête. Au minimum, les chaînes sont en minuscules, mais elles peuvent également être soumises à la lemmatisation et à l’arrêt de la suppression de mots. Les chaînes plus grandes ou les mots composés sont généralement fractionnés en fonction des espaces blancs, des traits d’union ou des tirets, et indexés comme des jetons distincts. 

Ce qu’il importe de retenir ici, c’est qu’il peut exister des différences entre ce que vous pensez que votre index contient et ce qu’il contient en réalité. Si les requêtes ne retournent pas les résultats attendus, vous pouvez inspecter les jetons créés par l’analyseur à l’aide de [Analyser le texte (API REST)](/rest/api/searchservice/test-analyzer). Pour plus d’informations sur la création de jetons et l’impact sur les requêtes, consultez [Recherche de termes partiels et modèles avec des caractères spéciaux](search-query-partial-matching.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une meilleure compréhension du fonctionnement d’une demande de requête, essayez les démarrages rapides suivants pour une expérience pratique.

+ [Navigateur de recherche](search-explorer.md)
+ [Guide pratique pour interroger dans REST](search-get-started-rest.md)
+ [Guide pratique pour interroger dans .NET](search-get-started-dotnet.md)
+ [Guide pratique pour interroger dans Python](search-get-started-python.md)
+ [Guide pratique pour interroger dans JavaScript](search-get-started-javascript.md)