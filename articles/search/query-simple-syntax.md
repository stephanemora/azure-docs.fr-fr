---
title: Syntaxe de requête simple
titleSuffix: Azure Cognitive Search
description: Référence pour la syntaxe des requêtes simples utilisée pour les requêtes de recherche en texte intégral dans la recherche cognitive Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: dfd75ad2c6ae246bfe6ee8b983744b3db07a841f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194939"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Syntaxe de requête simple dans la recherche cognitive Azure

La recherche cognitive Azure implémente deux langages de requête basés sur Lucene : L’[analyseur de requêtes simples](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) et l’[analyseur de requêtes Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

L’analyseur simple est plus flexible, et tente d’interpréter une requête même si elle n’est pas parfaitement composée. Pour cette raison, il s’agit de l’analyseur par défaut pour les requêtes dans Recherche cognitive Azure. 

La syntaxe simple est utilisée pour les expressions de requête passées dans le paramètre `search` d’une [requête de recherche dans des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents), et ne doit pas être confondue avec la [syntaxe OData](query-odata-filter-orderby-syntax.md) utilisée pour le paramètre d’[expressions $filter](search-filters.md) de la même API de recherche dans des documents. Les paramètres `search` et `$filter` ont une syntaxe différente, avec leurs propres règles pour la construction de requêtes, l’échappement de chaînes, et ainsi de suite.

Bien que l’analyseur simple soit basé sur la classe de l’[analyseur de requêtes simple Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html), l’implémentation dans Recherche cognitive Azure exclut la recherche approximative. Si vous avez besoin de la [recherche approximative](search-query-fuzzy.md) ou d’autres formes de requête avancée, utilisez plutôt la [syntaxe de requête Lucene complète](query-lucene-syntax.md).

## <a name="invoke-simple-parsing"></a>Appeler une analyse simple

La syntaxe simple est la syntaxe par défaut. Appeler celle-ci est nécessaire seulement si vous repassez de la syntaxe complète à la syntaxe simple. Pour définir explicitement la syntaxe, utilisez le paramètre de recherche `queryType`. Les valeurs valides sont `queryType=simple` ou `queryType=full`, `simple` correspondant à la valeur par défaut, et `full` appelle l'[analyseur de requêtes Lucene complètes](query-lucene-syntax.md) pour les requêtes plus avancées. 

## <a name="syntax-fundamentals"></a>Bases de la syntaxe

Tout texte avec un ou plusieurs termes est considéré comme un point de départ valide pour l’exécution des requêtes. La recherche cognitive Azure établit une correspondance avec les documents contenant tout ou partie des conditions, y compris d’éventuelles variations détectées lors de l’analyse du texte.

Aussi simple que cela puisse paraître, un aspect de l’exécution des requêtes dans la recherche cognitive Azure est qu’elles *peuvent* produire des résultats inattendus, en augmentant le nombre des résultats de la recherche au lieu de les diminuer quand vous ajoutez plus de conditions et d’opérateurs à la chaîne d’entrée. Cette expansion dépend de l'inclusion de l'opérateur NOT, combiné à une valeur de paramètre **searchMode** qui détermine comment NOT est interprété en termes de comportements AND ou OR. Pour plus d’informations, consultez [Opérateur NOT](#not-operator).

### <a name="precedence-operators-grouping"></a>Opérateurs de priorité (regroupement)

Vous pouvez utiliser des parenthèses pour créer des sous-requêtes, en incluant des opérateurs au sein de l’instruction entre parenthèses. Par exemple, `motel+(wifi|luxury)` recherche les documents contenant le terme « motel », et « wifi » ou « luxury » (ou les deux).

Le regroupement de champs est similaire, mais il délimite le regroupement à un seul champ. Par exemple, `hotelAmenities:(gym+(wifi|pool))` recherche « gym » et « wifi », ou « gym » et « pool », dans le champ « hotelAmenities ».  

### <a name="escaping-search-operators"></a>Échappement des opérateurs de recherche  

Dans la syntaxe simple, les opérateurs de recherche incluent les caractères suivants : `+ | " ( ) ' \`  

Si l’un de ces caractères fait partie d’un jeton dans l’index, échappez-le en le faisant précéder d’une barre oblique inverse (`\`) dans la requête. Par exemple, supposez que vous avez utilisé un analyseur personnalisé pour la création de jetons de termes entiers, et que votre index contient la chaîne « Luxury+Hotel ». Pour obtenir une correspondance exacte sur ce jeton, insérez un caractère d’échappement :  `search=luxury\+hotel`. 

Pour simplifier les choses pour les cas les plus classiques, il existe deux exceptions à cette règle où l’échappement n’est pas nécessaire :  

+ L’opérateur NOT `-` doit être placé en échappement seulement s’il s’agit du premier caractère après un espace. Si le `-` apparaît au milieu (par exemple `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`), aucun échappement n’est nécessaire.

+ L’opérateur de suffixe `*` doit être placé en échappement seulement s’il s’agit du dernier caractère avant un espace. Si le `*` apparaît au milieu (par exemple `4*4=16`), aucun échappement n’est nécessaire.

> [!NOTE]  
> Par défaut, l’analyseur standard supprime et coupe les mots sur les traits d’union, les espaces, les esperluettes et autres caractères pendant l’[analyse lexicale](search-lucene-query-architecture.md#stage-2-lexical-analysis). Si vous avez besoin que les caractères spéciaux restent dans la chaîne de requête, vous aurez peut-être besoin d’un analyseur qui les conserve dans l’index. Parmi les choix possibles figurent les [analyseurs de langage](index-add-language-analyzers.md) naturel de Microsoft, qui conservent les mots avec trait d'union, ou un analyseur personnalisé pour les modèles plus complexes. Pour plus d'informations, consultez [Termes partiels, modèles et caractères spéciaux](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Encodage de caractères dangereux et réservés dans les URL

Vérifiez que tous les caractères dangereux et réservés dans une URL sont encodés. Par exemple, « # » est un caractère non sécurisé, car il s'agit d'un identificateur de fragment/d'ancrage au sein d'une URL. Le caractère doit être encodé en `%23` s’il est utilisé dans une URL. « & » et « = » sont des exemples de caractères réservés, car ils délimitent les paramètres et spécifient des valeurs dans la Recherche cognitive Azure. Pour plus d’informations, consultez [RFC1738 : Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

Les caractères dangereux sont ``" ` < > # % { } | \ ^ ~ [ ]``. Les caractères réservés sont `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Limites de taille des requêtes

 Il existe une limite à la taille des requêtes que vous pouvez envoyer à la Recherche cognitive Azure. Plus précisément, vous pouvez avoir au maximum 1 024 clauses (des expressions séparées par AND, OR, etc.). Il existe également une limite d’environ 32 Ko pour la taille d’un terme individuel dans une requête. Si votre application génère des requêtes de recherche par programmation, nous vous recommandons de la concevoir de façon à ce qu’elle ne génère pas des requêtes d’une taille illimitée.  

## <a name="boolean-search"></a>Recherche booléenne

Vous pouvez incorporer des opérateurs booléens (AND, OR, NOT) dans une chaîne de requête pour créer un ensemble substantiel de critères en fonction desquels les documents correspondants sont trouvés. 

### <a name="and-operator-"></a>Opérateur AND `+`

L’opérateur AND est un signe plus. Par exemple, `wifi + luxury` recherche les documents contenant à la fois `wifi` et `luxury`.

### <a name="or-operator-"></a>Opérateur OR `|`

L’opérateur OR est une barre verticale. Par exemple, `wifi | luxury` recherche les documents contenant `wifi` ou `luxury`, ou les deux.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Opérateur NOT `-`

L’opérateur NOT est un signe moins. Par exemple, `wifi –luxury` recherche les documents qui contiennent le terme `wifi` et/ou pas le terme `luxury`.

Dans une requête, le paramètre **searchMode** détermine si un terme accompagné de l'opérateur NOT est associé à d'autres termes de la requête par les opérateurs AND ou OR (en supposant qu'il n'y ait pas d'opérateur `+` ou `|` sur les autres termes). Les valeurs valides sont `any` ou `all`.

`searchMode=any` augmente le rappel des requêtes en incluant plus de résultats, et par défaut `-` est interprété comme « OR NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme `wifi` ou avec ceux qui ne contiennent pas le terme `luxury`.

`searchMode=all` augmente la précision des requêtes en incluant moins de résultats et, par défaut, « - » est interprété comme « AND NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme `wifi` et ne contiennent pas le terme « luxury ». Il s’agit sans doute d’un comportement plus intuitif pour l’opérateur `-`. Ainsi, préférez `searchMode=all` à `searchMode=any` si vous souhaitez optimiser la précision des recherches plutôt que le rappel, *et* si vos utilisateurs utilisent fréquemment l'opérateur `-` dans les recherches.

Lorsque vous choisissez un paramètre **searchMode**, tenez compte des modèles d'interaction utilisateur pour les requêtes liées à différentes applications. Les utilisateurs qui recherchent des informations sont plus enclins à inclure un opérateur dans une requête, contrairement aux sites de commerce électronique qui disposent de structures de navigation intégrées.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Recherche par préfixe

L’opérateur de suffixe est un astérisque `*`. Par exemple, `lingui*` trouve « linguistique » ou « linguini », en ignorant la casse. 

Comme pour les filtres, une requête de préfixe recherche une correspondance exacte. Par conséquent, il n’existe aucun scoring de pertinence (tous les résultats reçoivent un score de recherche de 1.0). Les requêtes de préfixe peuvent être lentes, en particulier si l’index est volumineux et le préfixe constitué d’un petit nombre de caractères. 

Si vous souhaitez exécuter une requête de suffixe portant sur la dernière partie de la chaîne, utilisez une [recherche par caractères génériques](query-lucene-syntax.md#bkmk_wildcard) et la syntaxe Lucene complète.

## <a name="phrase-search-"></a>Recherche d'expressions `"`

Une recherche de termes est une requête portant sur un ou plusieurs termes et dans laquelle un des termes est considéré comme une correspondance. Une recherche d'expression est une expression exacte placée entre guillemets `" "`. Par exemple, si `Roach Motel` (sans guillemets) recherche les documents contenant `Roach` et/ou `Motel` n’importe où dans n’importe quel ordre, `"Roach Motel"` (avec des guillemets) établit une correspondance seulement avec les documents qui contiennent cette expression entière, avec les mots dans cet ordre (l’analyse de texte s’applique néanmoins toujours).

## <a name="see-also"></a>Voir aussi  

+ [Fonctionnement de la recherche en texte intégral dans la Recherche cognitive Azure](search-lucene-query-architecture.md)
+ [Exemples de requêtes pour une recherche simple](search-query-simple-examples.md)
+ [Exemples de requêtes pour une recherche Lucene complète](search-query-lucene-examples.md)
+ [API REST de recherche de documents](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxe de requête Lucene](query-lucene-syntax.md)
+ [Syntaxe d’expression OData](query-odata-filter-orderby-syntax.md) 
