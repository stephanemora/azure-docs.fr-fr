---
title: Utiliser la syntaxe de requête Lucene complète
titleSuffix: Azure Cognitive Search
description: Exemples de requête illustrant la syntaxe de requête Lucene pour la recherche approximative, la recherche de proximité, la promotion de termes, la recherche d’expressions régulières et la recherche par caractères génériques dans un index Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693558"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Utiliser la syntaxe de recherche Lucene « complète » (requêtes avancées dans Recherche cognitive Azure)

Lors de la création de requêtes pour la Recherche cognitive Azure, vous pouvez remplacer [l’analyseur de requêtes simple](query-simple-syntax.md) par défaut par [l’analyseur de requêtes Lucene](query-lucene-syntax.md), plus puissant, afin de formuler des expressions de requêtes spécialisées et avancées.

L’analyseur Lucene prend en charge des formats de requêtes complexes, telles que des requêtes portant sur des champs, la recherche approximative, la recherche par caractères génériques infixes et suffixes, la recherche de proximité, la promotion de termes et la recherche d’expression régulière. Cette fonctionnalité plus puissante nécessite des capacités de traitement supplémentaires et peut donc entraîner des temps d’exécution un peu plus longs. Dans cet article, vous pouvez parcourir des exemples décrivant les opérations de requête basées sur la syntaxe complète.

> [!Note]
> Une grande partie des constructions de requêtes spécialisées activées par le biais de la syntaxe de requête Lucene complète ne sont pas soumises à une [analyse du texte](search-lucene-query-architecture.md#stage-2-lexical-analysis), ce qui peut être surprenant si vous prévoyez une recherche de radical ou une lemmatisation. L’analyse lexicale est effectuée uniquement sur des termes complets (requête sur un terme ou une expression). Les types de requête avec des termes incomplets (requête de préfixe, de caractère générique, d’expression régulière, partielle) sont ajoutés directement à l’arborescence de requête, en ignorant la phase d’analyse. La seule transformation effectuée sur les termes de requête partiels est l’utilisation de minuscules. 
>

## <a name="hotels-sample-index"></a>Index hotels-sample

Les requêtes suivantes sont basées sur l’index hotels-sample, que vous pouvez créer en suivant les instructions de ce guide de [démarrage rapide](search-get-started-portal.md).

Les exemples de requêtes sont formulés à l’aide de l’API REST et des requêtes POST. Vous pouvez les coller et les exécuter dans [Postman](search-get-started-rest.md) ou dans [Visual Studio Code grâce à l’extension Recherche cognitive](search-get-started-vs-code.md).

Les en-têtes de requête doivent comporter les valeurs suivantes :

| Clé | Valeur |
|-----|-------|
| Content-Type | application/json|
| api-key  | `<your-search-service-api-key>`, clé de requête ou d’administration |

Les paramètres URI doivent inclure le point de terminaison de votre service de recherche avec le nom de l’index, les collections de documents, la commande de recherche et la version de l’API, comme dans l’exemple suivant :

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

Le corps de la demande doit être formé comme un JSON valide :

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ « search » défini sur `*` est une requête non spécifiée équivalente à une recherche null ou vide. Elle n’est pas particulièrement utile, mais c’est la recherche la plus simple que vous puissiez effectuer, et elle affiche tous les champs récupérables dans l’index, avec toutes les valeurs.

+ « queryType » défini sur « full » appelle l’analyseur de requêtes Lucene complet et est requis pour cette syntaxe.

+ « select » défini sur une liste de champs séparés par des virgules est utilisé pour la composition des résultats de la recherche, y compris uniquement les champs qui sont utiles pour les résultats de la recherche.

+ « count » renvoie le nombre de documents correspondant aux critères de recherche. Une chaîne de recherche vide comptabilisera tous les documents figurant dans l’index (environ 50 pour l’index hotels-sample).

## <a name="example-1-fielded-search"></a>Exemple 1 : Recherche par champ

La recherche par champ permet de restreindre les expressions de recherche individuelles et intégrées à un champ spécifique. Dans cet exemple, on recherche les noms d’hôtels contenant le terme « hôtel », mais pas « motel ». Vous pouvez spécifier plusieurs champs à l’aide de l’opérateur AND. 

Lorsque vous utilisez cette syntaxe de requête, vous pouvez omettre le paramètre « searchFields » lorsque les champs que vous souhaitez interroger se trouvent dans l’expression de recherche. Si vous incluez « searchFields » dans une recherche par champ, `fieldName:searchExpression` est toujours prioritaire par rapport à « searchFields ».

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

La réponse à cette requête doit ressembler à l’exemple suivant : filtré sur « Centre de balnéothérapie », renvoyant les noms d’hôtels contenant le terme « hôtel » ou « motel ».

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

L’expression de recherche peut être un mot ou une expression unique, ou bien une expression plus complexe entre parenthèses, éventuellement avec des opérateurs booléens. Voici quelques exemples :

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Veillez à ce que l’expression soit entre guillemets si vous souhaitez que les deux chaînes soient évaluées comme une seule entité, comme ici où deux emplacements distincts sont recherchés dans le champ Address/StateProvince. Selon le client, vous devrez peut-être placer les guillemets dans une séquence d’échappement (`\`).

Le champ spécifié dans `fieldName:searchExpression` doit être un champ pouvant faire l’objet d’une recherche. Pour en savoir plus sur l’attribution des définitions de champ, consultez la section [Créer un index (API REST)](/rest/api/searchservice/create-index).

## <a name="example-2-fuzzy-search"></a>Exemple 2 : Recherche approximative

Les recherches approximatives permettent d’obtenir des correspondances sur des termes similaires, y compris des mots mal orthographiés. Pour effectuer une recherche partielle, ajoutez le signe tilde `~` à la fin d’un mot avec un paramètre facultatif, une valeur comprise entre 0 et 2, qui spécifie la distance de modification. Par exemple, `blue~` ou `blue~1` retournent blue, blues et glue.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

La réponse à cette requête est « concierge » dans les documents correspondants, ajustés par souci de concision :

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Les phrases ne sont pas prises en charge directement, mais vous pouvez spécifier une correspondance partielle pour chaque terme d’une phrase à plusieurs éléments, par exemple `search=Tags:landy~ AND sevic~`.  Cette expression de requête renvoie 15 correspondances pour le terme « service de blanchisserie ».

> [!Note]
> Les requêtes approximatives ne sont pas [analysées](search-lucene-query-architecture.md#stage-2-lexical-analysis). Les types de requête avec des termes incomplets (requête de préfixe, de caractère générique, d’expression régulière, partielle) sont ajoutés directement à l’arborescence de requête, en ignorant la phase d’analyse. La seule transformation effectuée sur les termes de requête partiels est l’utilisation de minuscules.
>

## <a name="example-3-proximity-search"></a>Exemple 3 : Recherche de proximité

Les recherches de proximité servent à rechercher des termes qui sont proches les uns des autres dans un document. Insérez un signe tilde « ~ » à la fin d’une expression, suivi du nombre de mots qui créent la limite de proximité.

Cette requête recherche les termes « hôtel » et « aéroport » distants de 5 mots ou moins dans un document. Les guillemets sont placés dans une séquence d’échappement (`\"`) pour conserver l’expression :

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

La réponse à cette requête doit ressembler à l’exemple suivant :

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Exemple 4 : Promotion de termes

La promotion de termes signifie que vous pouvez accorder à un document un rang plus élevé s’il contient le terme promu, par rapport aux documents qui ne contiennent pas ce terme. Pour promouvoir un terme, utilisez le signe `^` (caret) avec un facteur de promotion (un nombre) à la fin du terme recherché. La valeur par défaut du facteur de promotion est 1. Si cette valeur doit être positive, elle peut néanmoins être inférieure à 1 (0,2 par exemple). Il ne faut pas confondre la promotion de termes avec les profils de score, qui promeuvent certains champs plutôt que des termes spécifiques.

Dans cette requête « avant », on recherche « accès à la plage » et on constate que sept documents correspondent à au moins un des deux termes.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

En réalité, seul un document correspond au terme « accès ». Étant donné qu’il s’agit de la seule correspondance, il figure en haut des résultats (deuxième position), même s’il ne contient pas le terme « plage ».

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

Dans la requête « après », on répète la recherche, cette fois en promouvant les résultats contenant le terme « accès » par rapport au terme « accès ». `search=Description:beach^2 access` est une version lisible de la requête. Selon votre client, vous devrez peut-être exprimer `^2` comme suit `%5E2`.

Suite à la promotion du terme « plage », la correspondance avec le Old Carrabelle Hotel passe en sixième position.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Exemple 5 : Regex

Une recherche d’expression régulière trouve une correspondance en fonction du contenu placé entre des barres obliques « / », comme le décrit la [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

La réponse à cette requête doit ressembler à l’exemple suivant :

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Les requêtes Regex ne sont pas [analysées](./search-lucene-query-architecture.md#stage-2-lexical-analysis). La seule transformation effectuée sur les termes de requête partiels est l’utilisation de minuscules.
>

## <a name="example-6-wildcard-search"></a>Exemple 6 : Recherche par caractères génériques

Vous pouvez utiliser la syntaxe généralement reconnue pour effectuer des recherches avec plusieurs caractères génériques (`*`) ou un caractère générique unique (`?`). Notez que l’Analyseur de requêtes Lucene prend en charge l’utilisation de ces symboles avec un terme unique, et non une expression.

Dans cette requête, recherchez les noms d’hôtels qui contiennent le préfixe « sc ». Vous ne pouvez pas utiliser un symbole `*` ou `?` comme premier caractère d’une recherche.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

La réponse à cette requête doit ressembler à l’exemple suivant :

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

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