---
title: 'Langage de requête Azure Cosmos DB : DEGREES'
description: Découvrez la fonction système SQL DEGREES dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c2109c2c11f137b1966741a95d1d0c02895016
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351215"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
 Retourne l’angle correspondant en degrés d’un angle spécifié en radians.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne le nombre de degrés d’un angle de PI/2 radians.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
