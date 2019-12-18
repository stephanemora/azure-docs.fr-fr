---
title: 'Langage de requête Azure Cosmos DB : ATAN'
description: Découvrir la fonction système SQL Arctangent (ATAN) dans Azure Cosmos DB, qui retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fd398e343a3e617ceb9f49ca8f31ee776a7f073
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873452"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée. Cette fonction est également appelée arctangente.  
  
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
  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
