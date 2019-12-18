---
title: 'Langage de requête Azure Cosmos DB : ENDSWITH'
description: Découvrir la fonction système SQL ENDSWITH dans Azure Cosmos DB, qui retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b3e692877faab8a8d507a44068d4cdfdc73a916
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873350"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Est une expression de chaîne.  
  
*str_expr2*  
   Est une expression de chaîne à comparer avec la fin de *str_expr1*.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant indique si « abc » se termine par « b » et « bc ».  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
