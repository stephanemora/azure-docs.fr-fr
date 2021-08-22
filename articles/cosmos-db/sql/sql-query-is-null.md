---
title: 'Langage de requête Azure Cosmos DB : IS_NULL'
description: Découvrez la fonction système SQL IS_NULL dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 330a94b233a50002e3f65482bac2da29cb9379cb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525709"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne une valeur booléenne indiquant si l’expression spécifiée est de type null.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
IS_NULL(<expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*expr*  
   Peut être toute expression.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, Null, objet, tableau et non défini à l’aide de la fonction `IS_NULL`.  
  
```sql
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index de plage](../index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de contrôle de type Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
