---
title: 'Langage de requête Azure Cosmos DB : REPLICATE'
description: Découvrez la fonction système SQL REPLICATE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349574"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICATE (Azure Cosmos DB)
 Répète une valeur de chaîne un nombre de fois spécifié.
  
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
  
## <a name="remarks"></a>Remarques
  La longueur maximale du résultat est 10 000 caractères : (length(*str_expr*)  *  *num_expr*) <= 10 000.

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
- [Présentation d’Azure Cosmos DB](introduction.md)
