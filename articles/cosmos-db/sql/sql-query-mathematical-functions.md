---
title: 'Langage de requête Azure Cosmos DB : fonctions mathématiques'
description: Découvrez les fonctions mathématiques dans Azure Cosmos DB, qui effectuent un calcul basé sur les valeurs d’entrée fournies comme arguments et renvoient une valeur numérique.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e3114220b6250afb9d633b073342ba6f2cf92b8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563574"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Fonctions mathématiques (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Chaque fonction mathématique effectue un calcul, basé sur les valeurs d’entrée fournies comme arguments, et renvoie une valeur numérique.

Vous pouvez exécuter des requêtes similaires à l’exemple suivant :

```sql
    SELECT VALUE ABS(-4)
```

Le résultat est le suivant :

```json
    [4]
```

## <a name="functions"></a>Fonctions

Les fonctions suivantes sont des fonctions mathématiques intégrées prises en charge, qui effectuent un calcul (généralement basé sur des arguments d’entrée) et retournent une expression numérique. La colonne **Utilisation de l’index** suppose, le cas échéant, que vous comparez la fonction système mathématique à une autre valeur avec un filtre d’égalité.
 
| Fonction système                 | Utilisation de l’index | [Utilisation de l’index dans les requêtes avec les fonctions d’agrégation scalaires](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | Notes                                                      |
| ------------------------------- | ----------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| [ABS](sql-query-abs.md)         | Recherche dans l'index  | Recherche dans l'index                                             |                                                              |
| [ACOS](sql-query-acos.md)       | Analyse complète   | Analyse complète                                              |                                                              |
| [ASIN](sql-query-asin.md)       | Analyse complète   | Analyse complète                                              |                                                              |
| [ATAN](sql-query-atan.md)       | Analyse complète   | Analyse complète                                              |                                                              |
| [ATN2](sql-query-atn2.md)       | Analyse complète   | Analyse complète                                              |                                                              |
| [CEILING](sql-query-ceiling.md) | Recherche dans l'index  | Recherche dans l'index                                             |                                                              |
| [COS](sql-query-cos.md)         | Analyse complète   | Analyse complète                                              |                                                              |
| [COT](sql-query-cot.md)         | Analyse complète   | Analyse complète                                              |                                                              |
| [DEGREES](sql-query-degrees.md) | Recherche dans l'index  | Recherche dans l'index                                             |                                                              |
| [EXP](sql-query-exp.md)         | Analyse complète   | Analyse complète                                              |                                                              |
| [FLOOR](sql-query-floor.md)     | Recherche dans l'index  | Recherche dans l'index                                             |                                                              |
| [LOG](sql-query-log.md)         | Analyse complète   | Analyse complète                                              |                                                              |
| [LOG10](sql-query-log10.md)     | Analyse complète   | Analyse complète                                              |                                                              |
| [PI](sql-query-pi.md)           | N/A         | N/A                                                    | PI() retourne une valeur constante. Étant donné que le résultat est déterministe, les comparaisons avec PI() peuvent utiliser l’index. |
| [POWER](sql-query-power.md)     | Analyse complète   | Analyse complète                                              |                                                              |
| [RADIANS](sql-query-radians.md) | Recherche dans l'index  | Recherche dans l'index                                             |                                                              |
| [RAND](sql-query-rand.md)       | N/A         | N/A                                                    | Rand() retourne un nombre aléatoire. Étant donné que le résultat n’est pas déterministe, les comparaisons qui impliquent Rand() ne peuvent pas utiliser l’index. |
| [ROUND](sql-query-round.md)     | Recherche dans l'index  | Recherche dans l'index                                             |                                                              |
| [SIGN](sql-query-sign.md)       | Recherche dans l'index  | Recherche dans l'index                                             |                                                              |
| [SIN](sql-query-sin.md)         | Analyse complète   | Analyse complète                                              |                                                              |
| [SQRT](sql-query-sqrt.md)       | Analyse complète   | Analyse complète                                              |                                                              |
| [SQUARE](sql-query-square.md)   | Analyse complète   | Analyse complète                                              |                                                              |
| [TAN](sql-query-tan.md)         | Analyse complète   | Analyse complète                                              |                                                              |
| [TRUNC](sql-query-trunc.md)     | Recherche dans l'index  | Recherche dans l'index                                              |                                                              |
## <a name="next-steps"></a>Étapes suivantes

- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
- [Fonctions définies par l’utilisateur](sql-query-udfs.md)
- [Agrégats](sql-query-aggregate-functions.md)
