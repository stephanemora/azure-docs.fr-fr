---
title: 'Langage de requête Azure Cosmos DB : ARRAY_LENGTH'
description: Découvrir la fonction système SQL Longueur de tableau dans Azure Cosmos DB, qui retourne le nombre d’éléments de l’expression de tableau spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9a8bf33befdd842a2979151fef3d54679ee03de1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871769"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 Retourne le nombre d’éléments de l’expression de tableau spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*arr_expr*  
   Est une expression de tableau.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment obtenir la longueur d’un tableau avec `ARRAY_LENGTH`.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de tableau Azure Cosmos DB](sql-query-array-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
