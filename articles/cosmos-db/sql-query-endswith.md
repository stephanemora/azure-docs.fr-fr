---
title: 'Langage de requête Azure Cosmos DB : ENDSWITH'
description: Découvrir la fonction système SQL ENDSWITH dans Azure Cosmos DB, qui retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299446"
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

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
