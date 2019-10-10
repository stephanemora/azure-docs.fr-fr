---
title: 'Langage de requête Azure Cosmos DB : SQUARE'
description: Découvrez la fonction système SQL SQUARE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683db82f2574da9150fb64767047d6ada6d667da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349419"
---
# <a name="square-azure-cosmos-db"></a>SQUARE (Azure Cosmos DB)
 Retourne le carré de la valeur numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SQUARE(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant renvoie les carrés des nombres de 1 à 3.  
  
```sql
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{s1: 1, s2: 4, s3: 9}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
