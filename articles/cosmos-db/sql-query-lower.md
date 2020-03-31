---
title: 'Langage de requête Azure Cosmos DB : LOWER'
description: Découvrir la fonction système SQL LOWER dans Azure Cosmos DB, qui retourne une expression de chaîne après la conversion des données caractères majuscules en minuscules
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302268"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Retourne une expression de chaîne après la conversion des caractères majuscules en caractères minuscules.  

La fonction système LOWER n’utilise pas l’index. Si vous envisagez d’effectuer des comparaisons non sensibles à la casse fréquentes, la fonction système LOWER peut consommer une quantité significative d’unités de requête. Si c’est le cas, au lieu d’utiliser la fonction système LOWER pour normaliser les données à chaque fois pour les comparaisons, vous pouvez normaliser la casse lors de l’insertion. Ensuite, une requête telle que SELECT * FROM c WHERE LOWER(c.name) = 'bob' devient simplement SELECT * FROM c WHERE c.name = 'bob'.

## <a name="syntax"></a>Syntaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment utiliser `LOWER` dans une requête.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
