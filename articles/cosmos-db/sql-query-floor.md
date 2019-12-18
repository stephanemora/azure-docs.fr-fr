---
title: 'Langage de requête Azure Cosmos DB : FLOOR'
description: Découvrez la fonction système SQL FLOOR dans Azure Cosmos DB, qui retourne le plus grand entier inférieur ou égal à l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8faf223a4871f6f109f9d133716989b1bcfdee08
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871174"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Cosmos DB)
 Retourne le plus grand nombre entier qui est inférieur ou égal à l'expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre des valeurs numériques positives, négatives et nulles avec la fonction `FLOOR`.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
