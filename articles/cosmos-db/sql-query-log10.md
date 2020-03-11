---
title: 'Langage de requête Azure Cosmos DB : LOG10'
description: Découvrez la fonction système SQL LOG10 dans Azure Cosmos DB, qui retourne le logarithme de base 10 de l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302489"
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
  
  Les fonctions LOG10 et POWER sont inversement proportionnelles. Par exemple, 10 ^ LOG10 (n) = n.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant déclare une variable et renvoie la valeur de LOG10 de la variable spécifiée (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
