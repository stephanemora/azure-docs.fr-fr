---
title: 'Langage de requête Azure Cosmos DB : POWER'
description: Découvrez la fonction système SQL POWER dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349635"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 Retourne la valeur de l’expression spécifiée élevée à la puissance spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr1*  
   Est une expression numérique.  
  
*numeric_expr2*  
   Est la puissance à laquelle élever *numeric_expr1*.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L'exemple suivant montre comment élever un nombre à la puissance 3 (le cube du nombre).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
