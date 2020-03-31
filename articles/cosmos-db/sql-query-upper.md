---
title: 'Langage de requête Azure Cosmos DB : UPPER'
description: Découvrez la fonction système SQL UPPER dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303968"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Retourne une expression de chaîne après la conversion des caractères minuscules en caractères majuscules.  

La fonction système UPPER n’utilise pas l’index. Si vous envisagez d’effectuer des comparaisons non sensibles à la casse fréquentes, la fonction système UPPER peut consommer une quantité significative de RU. Si c’est le cas, au lieu d’utiliser la fonction système UPPER pour normaliser les données à chaque fois pour les comparaisons, vous pouvez normaliser la casse lors de l’insertion. Ensuite, une requête telle que SELECT * FROM c WHERE UPPER(c.name) = 'BOB' devient simplement SELECT * FROM c WHERE c.name = 'BOB'.

## <a name="syntax"></a>Syntaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment utiliser `UPPER` dans une requête.  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
