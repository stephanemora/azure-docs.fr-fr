---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081830"
---
| Type de données | Fonctionnalités autorisées dans les expressions lambda avec `any` | Fonctionnalités autorisées dans les expressions lambda avec `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tout sauf `search.ismatch` et `search.ismatchscoring` | Identique |
| `Collection(Edm.String)` | Comparaisons avec `eq` ou `search.in` <br/><br/> Combinaison de sous-expressions avec `or` | Comparaisons avec `ne` ou `not search.in()` <br/><br/> Combinaison de sous-expressions avec `and` |
| `Collection(Edm.Boolean)` | Comparaisons avec `eq` ou `ne` | Identique |
| `Collection(Edm.GeographyPoint)` | Utilisation de `geo.distance` avec `lt` ou `le` <br/><br/> `geo.intersects` <br/><br/> Combinaison de sous-expressions avec `or` | Utilisation de `geo.distance` avec `gt` ou `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combinaison de sous-expressions avec `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Comparaisons avec `eq`, `ne`, `lt`, `gt`, `le` ou `ge` <br/><br/> Combinaison de comparaisons avec d’autres sous-expressions à l’aide de `or` <br/><br/> Combinaison de comparaisons, sauf `ne`, avec d’autres sous-expressions à l’aide de `and` <br/><br/> Expressions utilisant des combinaisons de `and` et `or` en [forme normale disjonctive (FND)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Comparaisons avec `eq`, `ne`, `lt`, `gt`, `le` ou `ge` <br/><br/> Combinaison de comparaisons avec d’autres sous-expressions à l’aide de `and` <br/><br/> Combinaison de comparaisons, sauf `eq`, avec d’autres sous-expressions à l’aide de `or` <br/><br/> Expressions utilisant des combinaisons de `and` et `or` en [forme normale conjonctive (FNC)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
