---
title: 'Langage de requête Azure Cosmos DB : SIN'
description: Découvrez la fonction système SQL SIN dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f96c1546ea75f7052c68b91c27437dd794c85ce
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525830"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne le sinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant calcule la valeur `SIN` de l’angle spécifié.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
