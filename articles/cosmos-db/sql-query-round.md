---
title: 'Langage de requête Azure Cosmos DB : ROUND'
description: Découvrez la fonction système SQL ROUND dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dc4d78f7af1086f9a4de9aa7392acb388df966e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590668"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourne une valeur numérique, arrondie au nombre entier le plus proche.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="remarks"></a>Notes
  
L’opération arrondit les valeurs médianes à la valeur la plus éloignée de zéro. Si l’entrée est une expression numérique qui se situe exactement entre deux entiers, le résultat est le nombre entier le plus éloigné de zéro. Cette fonction système bénéficiera d’un [index de plage](index-policy.md#includeexclude-strategy).
  
|<numeric_expr>|Arrondi|
|-|-|
|-6,5000|-7|
|-0,5|-1|
|0.5|1|
|6,5000|7|
  
## <a name="examples"></a>Exemples
  
L’exemple suivant arrondit les nombres positifs et négatifs suivants à l’entier le plus proche.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
Voici le jeu de résultats obtenu.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
