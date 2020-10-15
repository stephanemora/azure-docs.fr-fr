---
title: 'Langage de requête Azure Cosmos DB : COT'
description: Découvrez la fonction système SQL Cotangent(COT) dans Azure Cosmos DB, qui retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78299485"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant calcule la valeur `COT` de l’angle spécifié.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
