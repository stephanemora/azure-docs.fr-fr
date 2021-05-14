---
title: 'Langage de requête Azure Cosmos DB : LOWER'
description: Découvrir la fonction système SQL LOWER dans Azure Cosmos DB, qui retourne une expression de chaîne après la conversion des données caractères majuscules en minuscules
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b97b2806ebcf6613e9dde2e4d9172ee63e8ae719
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108164486"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

Cette fonction système [n’utilise pas d’index](index-overview.md#index-usage).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
