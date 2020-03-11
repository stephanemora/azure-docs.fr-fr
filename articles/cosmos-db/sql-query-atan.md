---
title: 'Langage de requête Azure Cosmos DB : ATAN'
description: Découvrir la fonction système SQL Arctangent (ATAN) dans Azure Cosmos DB, qui retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302676"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée. Cette valeur est également appelée arc tangente.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la valeur `ATAN` de la valeur spécifiée.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
