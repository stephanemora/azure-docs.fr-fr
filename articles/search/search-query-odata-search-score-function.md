---
title: 'Référence de la fonction search.score OData : Recherche Azure'
description: Fonction search.score OData dans les requêtes Recherche Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647514"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Fonction `search.score` OData dans Recherche Azure

Lorsque vous envoyez une requête à Recherche Azure sans le paramètre [ **$orderby** ](search-query-odata-orderby.md), les résultats renvoyés sont triés par score de pertinence (ordre décroissant). Même quand vous utilisez **$orderby**, le score de pertinence servira à rompre les liens par défaut. Toutefois, il est parfois utile d’utiliser le score de pertinence comme critère de tri initial, et d’utiliser d’autres critères comme critères décisifs. La fonction `search.score` vous permet de le faire.

## <a name="syntax"></a>Syntaxe

La syntaxe pour `search.score` dans **$orderby** est `search.score()`. La fonction `search.score` ne prend aucun paramètre. Elle peut être utilisée avec le spécificateur d’ordre de tri `asc` ou `desc`, tout comme les autres clauses dans le paramètre **$orderby**. Il peut apparaître n’importe où dans la liste des critères de tri.

## <a name="example"></a>Exemples

Trier les hôtels par ordre décroissant sur `search.score` et `rating`, puis par ordre croissant sur la distance à partir des coordonnées spécifiées, de sorte qu’entre deux d’hôtels avec une évaluation identique, le plus proche soit listé en premier :

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Étapes suivantes  

- [Vue d’ensemble du langage d’expression OData pour Recherche Azure](query-odata-filter-orderby-syntax.md)
- [Informations de référence sur la syntaxe d’expression OData pour Recherche Azure](search-query-odata-syntax-reference.md)
- [Rechercher des documents &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
