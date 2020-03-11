---
title: 'Langage de requête Azure Cosmos DB : STARTSWITH'
description: Découvrez la fonction système SQL STARTSWITH dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295706"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Retourne une valeur booléenne indiquant si la première expression de chaîne commence par la seconde.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Est une expression de chaîne.
  
*str_expr2*  
   Est une expression de chaîne à comparer avec le début de *str_expr1*.

## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant vérifie si la chaîne « abc » commence par « b » et « a ».  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
