---
title: 'Langage de requête Azure Cosmos DB : EndsWith'
description: Découvrir la fonction système SQL ENDSWITH dans Azure Cosmos DB, qui retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6e7d67757bd1cdf8b82e15a4c925d2716d414442
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533209"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Est une expression de chaîne.  
  
*str_expr2*  
   Est une expression de chaîne à comparer avec la fin de *str_expr1*.

*bool_expr* est une valeur facultative permettant d’ignorer la casse. Quand la valeur est « true », ENDSWITH effectue une recherche qui ne respecte pas la casse. Quand elle n’est pas spécifiée, cette valeur est « false ».
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
L’exemple suivant vérifie si la chaîne « abc » finit par « b » et « bC ».  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Notes

Découvrez comment [cette fonction système de chaîne utilise l’index](sql-query-string-functions.md).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
