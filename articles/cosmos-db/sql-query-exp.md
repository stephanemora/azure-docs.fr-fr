---
title: 'Langage de requête Azure Cosmos DB : EXP'
description: Découvrez la fonction système SQL Exposant (EXP) dans Azure Cosmos DB, qui retourne la valeur exponentielle de l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873316"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Retourne la valeur exponentielle de l'expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="remarks"></a>Notes
  
  La constante **e** (2,718281…), est la base des logarithmes népériens.  
  
  L’exposant d’un nombre correspond à la constante **e** élevée à la puissance de ce nombre. Par exemple, EXP(1.0) = e^1.0 = 2.71828182845905 et EXP(10) = e^10 = 22026.4657948067.  
  
  La valeur exponentielle du logarithme naturel d’un nombre est le nombre lui-même : EXP (LOG (n)) = n. Et le logarithme naturel de la valeur exponentielle d’un nombre est le nombre lui-même : LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant déclare une variable et renvoie la valeur exponentielle de la variable spécifiée (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 L’exemple suivant retourne la valeur exponentielle du logarithme naturel de 20 et le logarithme naturel de la valeur exponentielle de 20. Étant donné que ces fonctions sont l’inverse l’une de l’autre, la valeur renvoyée avec arrondi pour les opérations mathématiques à virgule flottante dans les deux cas est 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
