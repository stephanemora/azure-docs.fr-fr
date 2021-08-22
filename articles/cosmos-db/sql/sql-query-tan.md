---
title: 'Langage de requête Azure Cosmos DB : TAN'
description: Découvrez la fonction système SQL TAN dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 84731817f5ec2ab73c9a8089554508f39db35c70
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525690"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

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
- [Présentation d’Azure Cosmos DB](../introduction.md)
