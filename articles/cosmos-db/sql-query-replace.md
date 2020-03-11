---
title: 'Langage de requête Azure Cosmos DB : REPLACE'
description: Découvrez la fonction système SQL REPLACE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302200"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE (Azure Cosmos DB)
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
- [Présentation d’Azure Cosmos DB](introduction.md)
