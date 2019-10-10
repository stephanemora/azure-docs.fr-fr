---
title: 'Langage de requête Azure Cosmos DB : RAND'
description: Découvrez la fonction système SQL RAND dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4a0672000e630c9e06df84d9c2da5cb8b988c05a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349599"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Retourne une valeur numérique générée de façon aléatoire à partir de [0, 1).
 
## <a name="syntax"></a>Syntaxe
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Types de retour

  Renvoie une expression numérique.

## <a name="remarks"></a>Remarques

  `RAND` est une fonction non déterministe. Les appels répétitifs de `RAND` ne retournent pas les mêmes résultats.

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
- [Présentation d’Azure Cosmos DB](introduction.md)
