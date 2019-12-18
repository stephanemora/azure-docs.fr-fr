---
title: 'Langage de requête Azure Cosmos DB : CONCAT'
description: Découvrez la fonction système SQL CONCAT dans Azure Cosmos DB, qui retourne une chaîne issue de la concaténation de deux valeurs de chaîne ou plus
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adb8564d4addaaa3f4a383b32941549aed3d53bd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871548"
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
  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
