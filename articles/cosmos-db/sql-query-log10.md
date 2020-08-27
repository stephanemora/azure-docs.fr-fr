---
title: 'Langage de requête Azure Cosmos DB : LOG10'
description: Découvrez la fonction système SQL LOG10 dans Azure Cosmos DB, qui retourne le logarithme de base 10 de l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6869abb3040feb6431d60799536c9986c6ccb954
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798242"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Retourne le logarithme en base 10 de l'expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expression*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="remarks"></a>Notes
  
  Les fonctions LOG10 et POWER sont inversement proportionnelles. Par exemple, 10 ^ LOG10 (n) = n. Cette fonction système n’utilisera pas l’index.
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant déclare une variable et renvoie la valeur de LOG10 de la variable spécifiée (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
