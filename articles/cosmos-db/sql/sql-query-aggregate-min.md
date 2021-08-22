---
title: MIN dans le langage de requête Azure Cosmos DB
description: En savoir plus sur la fonction système Min (MIN) SQL dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 26cbc99c83acba404784ceeb54c8937391b3ce2b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525699"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cette fonction d’agrégation retourne la valeur minimale des valeurs de l’expression.
  
## <a name="syntax"></a>Syntaxe
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*scalar_expr*  
   Est une expression scalaire. 
  
## <a name="return-types"></a>Types de retour
  
Retourne une expression scalaire.  
  
## <a name="examples"></a>Exemples
  
L’exemple suivant retourne la valeur minimale de `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](../index-policy.md#includeexclude-strategy). Les arguments dans `MIN` peuvent être de type nombre, chaîne, booléen ou null. Toutes les valeurs non définies seront ignorées.

Lorsque vous comparez des données de types différents, l’ordre de priorité suivant est utilisé (dans l’ordre croissant) :

- null
- boolean
- nombre
- string

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques dans Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système dans Azure Cosmos DB](sql-query-system-functions.md)
- [Fonctions d’agrégation dans Azure Cosmos DB](sql-query-aggregate-functions.md)