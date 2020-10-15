---
title: 'Langage de requête Azure Cosmos DB : LEFT'
description: Découvrez la fonction système SQL LEFT dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303747"
---
# <a name="left-azure-cosmos-db"></a>LEFT (Azure Cosmos DB)
 Retourne la partie gauche d’une chaîne avec le nombre de caractères spécifié.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est l’expression de chaîne à partir de laquelle extraire des caractères.  
  
*num_expr*  
   Est une expression numérique qui spécifie le nombre de caractères.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la partie gauche de « abc » pour différentes valeurs de longueur.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
