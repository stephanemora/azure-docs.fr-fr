---
title: 'Langage de requête Azure Cosmos DB : IS_DEFINED'
description: Découvrez la fonction système SQL IS_DEFINED dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c3193262a41b3c6772d4fe29c78a132bc51bbd8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349876"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Retourne une valeur booléenne indiquant si une valeur a été attribuée à la propriété.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*expr*  
   Peut être toute expression.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant vérifie la présence d’une propriété dans le document JSON spécifié. La première fonction retourne la valeur true, car « a » est présent, mais la seconde retourne false, car « b » est absent.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de contrôle de type Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
