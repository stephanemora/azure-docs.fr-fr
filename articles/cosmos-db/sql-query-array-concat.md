---
title: 'Langage de requête Azure Cosmos DB : ARRAY_CONCAT'
description: Découvrez la fonction système SQL ARRAY_CONCAT dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ad973650ac205313f9045c170f99e15e385a82d1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348718"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Retourne un tableau qui est le résultat de la concaténation d’au moins deux valeurs de tableau.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Arguments
  
*arr_expr*  
   Est une expression de tableau à concaténer aux autres valeurs. La fonction `ARRAY_CONCAT` nécessite au moins deux arguments *arr_expr*.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de tableau.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment concaténer deux tableaux.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de tableau Azure Cosmos DB](sql-query-array-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
