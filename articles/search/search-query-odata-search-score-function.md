---
title: Référence de la fonction search.score OData
titleSuffix: Azure Cognitive Search
description: Documentation de syntaxe et de référence pour l’utilisation de la fonction search.score dans les requêtes Recherche cognitive Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: c31304228d9629b0df7f7511ecca2616b4891ee7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206954"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Fonction `search.score` OData dans Recherche cognitive Azure

Lorsque vous envoyez une requête à Recherche cognitive Azure sans le [paramètre **$orderby**](search-query-odata-orderby.md), les résultats renvoyés sont triés par score décroissant de pertinence. Même quand vous utilisez **$orderby**, le score de pertinence servira à rompre les liens par défaut. Toutefois, il est parfois utile d’utiliser le score de pertinence comme critère de tri initial, et d’utiliser d’autres critères comme critères décisifs. La fonction `search.score` vous permet de le faire.

## <a name="syntax"></a>Syntaxe

La syntaxe pour `search.score` dans **$orderby** est `search.score()`. La fonction `search.score` ne prend aucun paramètre. Elle peut être utilisée avec le spécificateur d’ordre de tri `asc` ou `desc`, tout comme les autres clauses dans le paramètre **$orderby**. Il peut apparaître n’importe où dans la liste des critères de tri.

## <a name="example"></a>Exemple

Trier les hôtels par ordre décroissant sur `search.score` et `rating`, puis par ordre croissant sur la distance à partir des coordonnées spécifiées, de sorte qu’entre deux d’hôtels avec une évaluation identique, le plus proche soit listé en premier :

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Étapes suivantes  

- [Vue d’ensemble du langage d’expression OData pour Recherche cognitive Azure](query-odata-filter-orderby-syntax.md)
- [Informations de référence sur la syntaxe d’expression OData pour Recherche cognitive Azure](search-query-odata-syntax-reference.md)
- [Rechercher des documents &#40;API REST Recherche cognitive Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
