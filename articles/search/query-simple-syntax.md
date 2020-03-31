---
title: Syntaxe de requête simple
titleSuffix: Azure Cognitive Search
description: Référence pour la syntaxe des requêtes simples utilisée pour les requêtes de recherche en texte intégral dans la recherche cognitive Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152667"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Syntaxe de requête simple dans la recherche cognitive Azure

La recherche cognitive Azure implémente deux langages de requête basés sur Lucene : L’[analyseur de requêtes simples](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) et l’[analyseur de requêtes Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Dans la recherche cognitive Azure, la syntaxe des requêtes simples exclut les options fuzzy/slop.  

> [!NOTE]
> La syntaxe de requête simple est utilisée pour les expressions de requête passées dans le paramètre **search** de l’API [Recherche dans des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) et ne doit pas être confondue avec la [syntaxe OData](query-odata-filter-orderby-syntax.md) utilisée pour le paramètre [$Filter](search-filters.md) de cette API. Ces différentes syntaxes ont leurs propres règles pour la construction de requêtes, l’échappement de chaînes, etc.
>
> Recherche cognitive Azure fournit une autre [syntaxe des requêtes Lucene](query-lucene-syntax.md) pour les requêtes plus complexes dans le paramètre **search**. Pour plus d’informations sur l’architecture de l’analyse des requêtes et les avantages de chacune des syntaxes, consultez [Fonctionnement de la recherche en texte intégral dans la recherche cognitive Azure](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Comment appeler l’analyse simple

La syntaxe simple est la syntaxe par défaut. Appeler celle-ci est nécessaire seulement si vous repassez de la syntaxe complète à la syntaxe simple. Pour définir explicitement la syntaxe, utilisez le paramètre de recherche `queryType`. Les valeurs valides sont `simple|full`, `simple` étant la valeur par défaut et `full` la valeur pour Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalies de comportement des requêtes

Tout texte avec un ou plusieurs termes est considéré comme un point de départ valide pour l’exécution des requêtes. La recherche cognitive Azure établit une correspondance avec les documents contenant tout ou partie des conditions, y compris d’éventuelles variations détectées lors de l’analyse du texte. 

Aussi simple que cela puisse paraître, un aspect de l’exécution des requêtes dans la recherche cognitive Azure est qu’elles *peuvent* produire des résultats inattendus, en augmentant le nombre des résultats de la recherche au lieu de les diminuer quand vous ajoutez plus de conditions et d’opérateurs à la chaîne d’entrée. Le fait que cette expansion se produit ou non dépend de l’inclusion d’un opérateur NOT, combiné avec une valeur pour le paramètre `searchMode` qui détermine comment NOT est interprété en termes de comportements de AND et de OR. Étant donnée la valeur par défaut, `searchMode=Any`, et un opérateur NOT, l’opération est considérée comme une action OR : ainsi, `"New York" NOT Seattle` retourne toutes les villes qui ne sont pas Seattle.  

En règle générale, vous voyez plus probablement ces comportements dans les modèles d’interaction utilisateur pour les applications qui recherchent de contenu, où les utilisateurs sont plus susceptibles d’inclure un opérateur dans une requête, contrairement aux sites de e-commerce qui ont des structures de navigation plus intégrées. Pour plus d’informations, consultez [Opérateur NOT](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Opérateurs logiques (AND, OR, NOT) 

Vous pouvez incorporer des opérateurs dans une chaîne de requête pour créer un ensemble substantiel de critères pour lesquels des documents correspondants sont trouvés. 

### <a name="and-operator-"></a>Opérateur AND `+`

L’opérateur AND est un signe plus. Par exemple, `wifi+luxury` recherche les documents contenant à la fois `wifi` et `luxury`.

### <a name="or-operator-"></a>Opérateur OR `|`

L’opérateur OR est une barre verticale. Par exemple, `wifi | luxury` recherche les documents contenant `wifi` ou `luxury`, ou les deux.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Opérateur NOT `-`

L’opérateur NOT est un signe moins. Par exemple, `wifi –luxury` recherche les documents qui ont le terme `wifi` et/ou qui n’ont pas `luxury` (et/ou est contrôlé par `searchMode`).

> [!NOTE]  
>  L’option `searchMode` contrôle si un terme avec l’opérateur NOT est soumis à l’opération logique AND ou OR avec les autres termes de la requête en l’absence d’un opérateur `+` ou `|`. Rappelez-vous que `searchMode` peut être défini sur `any` (la valeur par défaut) ou sur `all`. Si vous utilisez `any`, cela augmente le rappel des requêtes en incluant plus de résultats, et par défaut, `-` est interprété comme « OR NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme `wifi` ou avec ceux qui ne contiennent pas le terme `luxury`. Si vous utilisez `all`, cela augmente la précision des requêtes en incluant moins de résultats, et par défaut, « - » est interprété comme « AND NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme `wifi` et ne contiennent pas le terme « luxury ». Il s’agit sans doute d’un comportement plus intuitif pour l’opérateur `-`. Ainsi, vous pouvez préférer `searchMode=all` à `searchMode=any` si vous voulez optimiser les recherches pour la précision au lieu du rappel *et* si vos utilisateurs utilisent fréquemment l’opérateur `-` dans les recherches.

## <a name="suffix-operator"></a>Opérateur de suffixe

L’opérateur de suffixe est un astérisque `*`. Par exemple, `lux*` recherche les documents contenant un terme qui commence par `lux`, indépendamment de la casse.  

## <a name="phrase-search-operator"></a>Opérateur de recherche d’expression

L’opérateur d’expression place une expression entre guillemets `" "`. Par exemple, si `Roach Motel` (sans guillemets) recherche les documents contenant `Roach` et/ou `Motel` n’importe où dans n’importe quel ordre, `"Roach Motel"` (avec des guillemets) établit une correspondance seulement avec les documents qui contiennent cette expression entière, avec les mots dans cet ordre (l’analyse de texte s’applique néanmoins toujours).

## <a name="precedence-operator"></a>Opérateur de priorité

L’opérateur de priorité place la chaîne entre parenthèses `( )`. Par exemple, `motel+(wifi | luxury)` recherche les documents contenant le terme « motel », et `wifi` ou `luxury` (ou les deux).  

## <a name="escaping-search-operators"></a>Échappement des opérateurs de recherche  

 Pour pouvoir utiliser les symboles ci-dessus comme faisant partie du texte recherché, ils doivent être placés en échappement en les faisant précéder d’une barre oblique inverse. Par exemple, `luxury\+hotel` produit le terme `luxury+hotel`. Afin de simplifier les choses pour les cas les plus classiques, il existe deux exceptions à cette règle où l’échappement n’est pas nécessaire :  

- L’opérateur NOT `-` doit être placé en échappement seulement s’il est le premier caractère après un espace, et donc pas s’il est au milieu d’un terme. Par exemple, `wi-fi` est un seul terme, alors que les GUID (comme `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) sont traités comme un seul jeton.
- L’opérateur de suffixe `*` doit être placé en échappement seulement s’il est le dernier caractère avant un espace, et donc pas s’il est au milieu d’un terme. Par exemple, `wi*fi` est traité comme un seul jeton.

> [!NOTE]  
>  Bien que le placement en échappement conserve les jetons ensemble, l’analyse de texte peut les fractionner, selon le mode d’analyse. Pour plus d’informations, consultez [Support linguistique &#40;API REST de la recherche cognitive Azure&#41;](index-add-language-analyzers.md).  

## <a name="see-also"></a>Voir aussi  

+ [Rechercher des documents &#40;API REST de la recherche cognitive Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe de requête Lucene](query-lucene-syntax.md)
+ [Syntaxe d’expression OData](query-odata-filter-orderby-syntax.md) 
