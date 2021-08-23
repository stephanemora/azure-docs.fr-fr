---
title: 'Langage de requête Azure Cosmos DB : REPLICATE'
description: Découvrez la fonction système SQL REPLICATE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b496f8014630595fbde97f65941b6caa62be5372
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533328"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICATE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Répète une valeur de chaîne un nombre spécifié de fois.
  
## <a name="syntax"></a>Syntaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne.
  
*num_expr*  
   Est une expression numérique. Si *num_expr* est négative ou non finie, le résultat est indéfini.
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.
  
## <a name="remarks"></a>Notes

  La longueur maximale du résultat est 10 000 caractères : (length(*str_expr*)  *  *num_expr*) <= 10 000. Cette fonction système n’utilisera pas l’index.

## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment utiliser `REPLICATE` dans une requête.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Voici le jeu de résultats obtenu.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
