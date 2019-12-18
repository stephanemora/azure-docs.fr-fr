---
title: 'Langage de requête Azure Cosmos DB : ARRAY_CONCAT'
description: Découvrir la fonction système SQL de concaténation de tableaux dans Azure Cosmos DB, qui retourne un tableau issu de la concaténation de deux valeurs de tableau ou plus
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 10370e16c95f4fc747dd3a66a56794da38562972
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871820"
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
