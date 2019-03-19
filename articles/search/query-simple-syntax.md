---
title: Syntaxe des requêtes simples - Recherche Azure
description: Informations de référence pour la syntaxe des requêtes simples utilisée pour les requêtes de recherche en texte intégral dans Recherche Azure.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/31/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 4f06af8044a79a7dc54d6fde55992111d24d22a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441558"
---
# <a name="simple-query-syntax-in-azure-search"></a>Syntaxe des requêtes simples dans Recherche Azure
Recherche Azure implémente deux langages de requête basés sur Lucene : L’[analyseur de requêtes simples](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) et l’[analyseur de requêtes Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Dans Recherche Azure, la syntaxe des requêtes simples exclut les options fuzzy/slop.  

> [!NOTE]  
>  Recherche Azure fournit une autre [syntaxe des requêtes Lucene](query-lucene-syntax.md) pour les requêtes plus complexes. Pour plus d’informations sur l’architecture de l’analyse des requêtes et les avantages de chacune des syntaxes de requête, consultez [Fonctionnement de la recherche en texte intégral dans Recherche Azure](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

## <a name="how-to-invoke-simple-parsing"></a>Comment appeler l’analyse simple

La syntaxe simple est la syntaxe par défaut. Appeler celle-ci est nécessaire seulement si vous repassez de la syntaxe complète à la syntaxe simple. Pour définir explicitement la syntaxe, utilisez le paramètre de recherche `queryType`. Les valeurs valides sont `simple|full`, `simple` étant la valeur par défaut et `full` la valeur pour Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalies de comportement des requêtes

Tout texte avec un ou plusieurs termes est considéré comme un point de départ valide pour l’exécution des requêtes. Recherche Azure établit une correspondance avec les documents contenant tout ou partie des termes, y compris d’éventuelles variations détectées lors de l’analyse du texte. 

Aussi simple que cela puisse paraître, un aspect de l’exécution des requêtes dans Recherche Azure est qu’elles *peuvent* produire des résultats inattendus, en augmentant le nombre des résultats de la recherche au lieu de les diminuer quand vous ajoutez plus de termes et d’opérateurs à la chaîne d’entrée. Le fait que cette expansion se produit ou non dépend de l’inclusion d’un opérateur NOT, combiné avec une valeur pour le paramètre `searchMode` qui détermine comment NOT est interprété en termes de comportements de AND et de OR. Étant donnée la valeur par défaut, `searchMode=Any`, et un opérateur NOT, l’opération est considérée comme une action OR : ainsi, `"New York" NOT Seattle` retourne toutes les villes qui ne sont pas Seattle.  

En règle générale, vous voyez plus probablement ces comportements dans les modèles d’interaction utilisateur pour les applications qui recherchent de contenu, où les utilisateurs sont plus susceptibles d’inclure un opérateur dans une requête, contrairement aux sites de e-commerce qui ont des structures de navigation plus intégrées. Pour plus d’informations, consultez [Opérateur NOT](#not-operator). 

## <a name="operators-in-simple-search"></a>Opérateurs dans une recherche simple

Vous pouvez incorporer des opérateurs dans une chaîne de requête pour générer un ensemble de critères par rapport à laquelle sont trouvent les documents correspondants. 

## <a name="and-operator-"></a>Opérateur AND `+`

L’opérateur AND est un signe plus. Par exemple, `wifi+luxury` recherche les documents contenant à la fois `wifi` et `luxury`.

## <a name="or-operator-"></a>Opérateur OR `|`

L’opérateur OR est une barre verticale. Par exemple, `wifi | luxury` recherche les documents contenant `wifi` ou `luxury`, ou les deux.

<a name="not-operator"></a>

## <a name="not-operator--"></a>Opérateur NOT `-`

L’opérateur NOT est un signe moins. Par exemple, `wifi –luxury` recherche les documents qui ont le terme `wifi` et/ou qui n’ont pas `luxury` (et/ou est contrôlé par `searchMode`).

> [!NOTE]  
>  L’option `searchMode` contrôle si un terme avec l’opérateur NOT est soumis à l’opération logique AND ou OR avec les autres termes de la requête en l’absence d’un opérateur `+` ou `|`. Rappelez-vous que `searchMode` peut être défini sur `any` (la valeur par défaut) ou sur `all`. Si vous utilisez `any`, cela augmente le rappel des requêtes en incluant plus de résultats, et par défaut, `-` est interprété comme « OR NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme `wifi` ou avec ceux qui ne contiennent pas le terme `luxury`. Si vous utilisez `all`, cela augmente la précision des requêtes en incluant moins de résultats, et par défaut, « - » est interprété comme « AND NOT ». Par exemple, `wifi -luxury` établit une correspondance avec les documents qui contiennent le terme `wifi` et ne contiennent pas le terme « luxury ». Il s’agit sans doute d’un comportement plus intuitif pour l’opérateur `-`. Ainsi, vous pouvez préférer `searchMode=all` à `searchMode=any` si vous voulez optimiser les recherches pour la précision au lieu du rappel *et* si vos utilisateurs utilisent fréquemment l’opérateur `-` dans les recherches.

## <a name="suffix-operator-"></a>Opérateur de suffixe `*`

L’opérateur de suffixe est un astérisque. Par exemple, `lux*` recherche les documents contenant un terme qui commence par `lux`, indépendamment de la casse.  

## <a name="phrase-search-operator--"></a>Opérateur de recherche d’expression `" "`

L’opérateur d’expression place une expression entre des guillemets. Par exemple, si `Roach Motel` (sans guillemets) recherche les documents contenant `Roach` et/ou `Motel` n’importe où dans n’importe quel ordre, `"Roach Motel"` (avec des guillemets) établit une correspondance seulement avec les documents qui contiennent cette expression entière, avec les mots dans cet ordre (l’analyse de texte s’applique néanmoins toujours).

## <a name="precedence-operator--"></a>Opérateur de priorité `( )`

L’opérateur de priorité place la chaîne entre des parenthèses. Par exemple, `motel+(wifi | luxury)` recherche les documents contenant le terme « motel », et `wifi` ou `luxury` (ou les deux).  

## <a name="escaping-search-operators"></a>Échappement des opérateurs de recherche  

 Pour pouvoir utiliser les symboles ci-dessus comme faisant partie du texte recherché, ils doivent être placés en échappement en les faisant précéder d’une barre oblique inverse. Par exemple, `luxury\+hotel` produit le terme `luxury+hotel`. Afin de simplifier les choses pour les cas les plus classiques, il existe deux exceptions à cette règle où l’échappement n’est pas nécessaire :  

- L’opérateur NOT `-` doit être placé en échappement seulement s’il est le premier caractère après un espace, et donc pas s’il est au milieu d’un terme. Par exemple, `wi-fi` est un seul terme, alors que les GUID (comme `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) sont traités comme un seul jeton.
- L’opérateur de suffixe `*` doit être placé en échappement seulement s’il est le dernier caractère avant un espace, et donc pas s’il est au milieu d’un terme. Par exemple, `wi*fi` est traité comme un seul jeton.

> [!NOTE]  
>  Bien que le placement en échappement conserve les jetons ensemble, l’analyse de texte peut les fractionner, selon le mode d’analyse. Pour plus d’informations, consultez [Prise en charge linguistique &#40;API REST du service Recherche Azure&#41;](index-add-language-analyzers.md).  

## <a name="see-also"></a>Voir aussi  

+ [Rechercher des documents &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe de requête Lucene](query-lucene-syntax.md)
+ [Syntaxe d’expression OData](query-odata-filter-orderby-syntax.md) 
