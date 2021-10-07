---
title: 'Langage de requête Azure Cosmos DB : RTRIM'
description: Découvrez la fonction système SQL RTRIM dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f1dfaa8de0f6fa6d04fe91618a68a79d4121b7bd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647760"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne une expression de chaîne après suppression de l’espace de fin ou les caractères spécifiés.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
RTRIM(<str_expr1>[, <str_expr2>])  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Est une expression de chaîne

*str_expr2*  
   Est une expression de chaîne facultative à tronquer de str_expr1. Si elle n’est pas définie, la valeur par défaut est un espace.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple ci-après indique comment utiliser `RTRIM` dans une requête.  
  
```sql
SELECT RTRIM("   abc") AS t1, 
RTRIM("   abc   ") AS t2, 
RTRIM("abc   ") AS t3, 
RTRIM("abc") AS t4,
RTRIM("abc", "bc") AS t5,
RTRIM("abc", "abc") AS t6
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[
    {
        "t1": "   abc",
        "t2": "   abc",
        "t3": "abc",
        "t4": "abc",
        "t5": "a",
        "t6": ""
    }
]
``` 

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
