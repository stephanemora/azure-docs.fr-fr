---
title: 'Langage de requête Azure Cosmos DB : CONCAT'
description: Découvrez la fonction système SQL CONCAT dans Azure Cosmos DB, qui retourne une chaîne issue de la concaténation de deux valeurs de chaîne ou plus
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302608"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Retourne une chaîne qui est le résultat de la concaténation d’au moins deux valeurs de chaîne.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne à concaténer aux autres valeurs. La fonction `CONCAT` nécessite au moins deux arguments *str_expr*.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant renvoie la chaîne concaténée des valeurs spécifiées.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
