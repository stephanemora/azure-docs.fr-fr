---
title: 'Langage de requête Azure Cosmos DB : ASIN'
description: Découvrez la fonction système SQL arc sinus (ASIN) dans Azure Cosmos DB, qui retourne l’angle, en radians, dont le sinus est l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302693"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Retourne l’angle, en radians, dont le sinus est l’expression numérique spécifiée. Cette fonction est également appelée arcsinus.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la valeur `ASIN` de -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
