---
title: 'Langage de requête Azure Cosmos DB : ACOS'
description: Découvrez la fonction système SQL ACOS (arccosice) dans Azure Cosmos DB, qui retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300959"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelée arccosinus.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la valeur `ACOS` de -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
