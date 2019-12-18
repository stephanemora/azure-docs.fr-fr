---
title: 'Langage de requête Azure Cosmos DB : CONTAINS'
description: Découvrez la fonction système SQL CONTAINS dans Azure Cosmos DB, qui retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 59c0e2628bfde24200bd386adc858ba778790231
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871531"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
 Retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr1*  
   Est l’expression de chaîne faisant l’objet de la recherche.  
  
*str_expr2*  
   Est l’expression de chaîne à rechercher.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant vérifie si « abc » contient « ab » et « d ».  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
