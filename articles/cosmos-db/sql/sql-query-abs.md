---
title: 'Langage de requête Azure Cosmos DB : ABS'
description: En savoir plus sur la façon dont la fonction système SQL absolu (ABS) dans Azure Cosmos DB retourne la valeur positive de l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 382252f0a7361a15cc5dc4bc14929e905bd72310
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533190"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne la valeur (positive) absolue de l'expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre les résultats de l’utilisation de la fonction `ABS` sur trois nombres différents.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](../index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
