---
title: 'Langage de requête Azure Cosmos DB : CONCAT'
description: Découvrez la fonction système SQL CONCAT dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351265"
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
