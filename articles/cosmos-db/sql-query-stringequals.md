---
title: 'Langage de requête Azure Cosmos DB : StringEquals'
description: Découvrir comment la fonction système SQL StringEquals dans Azure Cosmos D retourne une valeur booléenne indiquant si la première expression de chaîne correspond à la seconde
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: e8e24474cea4607281baf9c79ad20a465b3dfaef
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165008"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourne une valeur booléenne indiquant si la première expression de chaîne correspond à la seconde.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Première expression de chaîne à comparer.  
  
*str_expr2*  
   Seconde expression de chaîne à comparer.  

*bool_expr* Valeur facultative permettant d’ignorer la casse. Quand la valeur est « true », StringEquals effectue une recherche qui ne respecte pas la casse. Quand elle n’est pas spécifiée, cette valeur est « false ».
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant vérifie si « abc » correspond à « abc », et si « abc » correspond à « ABC ».  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Notes

Découvrez comment [cette fonction système de chaîne utilise l'index](sql-query-string-functions.md).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
