---
title: 'Langage de requête Azure Cosmos DB : DEGREES'
description: Découvrez la fonction système SQL DEGREES dans Azure Cosmos DB, qui retourne l’angle correspondant en degrés pour un angle spécifié en radians
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5c7aa5496533a42ad726f9ed8efd8ed7429375fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338881"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
