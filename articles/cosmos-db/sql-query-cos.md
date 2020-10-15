---
title: 'Langage de requête Azure Cosmos DB : COS'
description: Découvrez comment la fonction système SQL Cosine (COS) dans Azure Cosmos DB retourne le cosinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78304019"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Retourne le cosinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant calcule la valeur `COS` de l’angle spécifié.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
