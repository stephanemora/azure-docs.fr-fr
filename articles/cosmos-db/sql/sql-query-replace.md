---
title: 'Langage de requête Azure Cosmos DB : REPLACE'
description: Découvrez la fonction système SQL REPLACE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 82d8417470263374f75171683abb0b8b8ea9ab99
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533332"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Remplace toutes les occurrences d'une valeur de type chaîne spécifiée par une autre valeur de type chaîne.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Est l’expression de chaîne faisant l’objet de la recherche.  
  
*str_expr2*  
   Est l’expression de chaîne à rechercher.  
  
*str_expr3*  
   Est l’expression de chaîne remplaçant les occurrences de *str_expr2* dans *str_expr1*.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment utiliser `REPLACE` dans une requête.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
