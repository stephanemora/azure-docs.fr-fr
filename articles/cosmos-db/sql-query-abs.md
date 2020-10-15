---
title: 'Langage de requête Azure Cosmos DB : ABS'
description: En savoir plus sur la façon dont la fonction système SQL absolu (ABS) dans Azure Cosmos DB retourne la valeur positive de l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6d173827f2695cc20fa208d390731acf0edb3848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78301095"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
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

Cette fonction système bénéficiera d’un [index de plage](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
