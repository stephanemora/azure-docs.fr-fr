---
title: 'Langage de requête Azure Cosmos DB : COT'
description: Découvrez la fonction système SQL Cotangent(COT) dans Azure Cosmos DB, qui retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871497"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant calcule la valeur `COT` de l’angle spécifié.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
