---
title: 'Langage de requête Azure Cosmos DB : RAND'
description: Découvrez la fonction système SQL RAND dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cd903a6f067c116277a7ec3c2d7d01ce8e2dedad
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533252"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne une valeur numérique générée de façon aléatoire à partir de [0, 1).
 
## <a name="syntax"></a>Syntaxe
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Types de retour

  Renvoie une expression numérique.

## <a name="remarks"></a>Notes

  `RAND` est une fonction non déterministe. Les appels répétitifs de `RAND` ne retournent pas les mêmes résultats. Cette fonction système n’utilisera pas l’index.


## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne une valeur numérique générée de façon aléatoire.
  
```sql
SELECT RAND() AS rand 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
