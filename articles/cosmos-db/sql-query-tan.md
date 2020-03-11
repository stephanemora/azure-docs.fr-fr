---
title: 'Langage de requête Azure Cosmos DB : TAN'
description: Découvrez la fonction système SQL TAN dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301979"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Retourne la tangente de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant calcule la tangente de PI()/ 2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
