---
title: 'Langage de requête Azure Cosmos DB : StartsWith'
description: Découvrez la fonction système SQL STARTSWITH dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d850ae0a01a93e2214d4dc26df98234f54e28a8b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533347"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne une valeur booléenne indiquant si la première expression de chaîne commence par la seconde.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Est une expression de chaîne.
  
*str_expr2*  
   Est une expression de chaîne à comparer avec le début de *str_expr1*.

*bool_expr* est une valeur facultative permettant d’ignorer la casse. Quand la valeur est « true », StartsWith effectue une recherche qui ne respecte pas la casse. Lorsqu’elle n’est pas spécifiée, la valeur est « false ».

## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
L’exemple suivant vérifie si la chaîne « abc » commence par « b » et « A ».  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>Notes

Découvrez comment [cette fonction système de chaîne utilise l’index](sql-query-string-functions.md).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
