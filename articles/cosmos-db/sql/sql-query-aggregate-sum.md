---
title: Fonction SUM dans le langage de requête Azure Cosmos DB
description: En savoir plus sur la fonction système Sum (SUM) SQL dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 77f00474a53b3fa068989a111517dc05806a1ed4
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525760"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cette fonction d’agrégation retourne la somme des valeurs de l’expression.
  
## <a name="syntax"></a>Syntaxe
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
L’exemple suivant retourne la somme de `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](../index-policy.md#includeexclude-strategy). Si des arguments dans `SUM` sont de type chaîne, booléen ou null, l’ensemble de la fonction système d’agrégation retourne `undefined`. Si un argument a une valeur `undefined`, il n’a pas d’impact sur le calcul de `SUM`.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques dans Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système dans Azure Cosmos DB](sql-query-system-functions.md)
- [Fonctions d’agrégation dans Azure Cosmos DB](sql-query-aggregate-functions.md)