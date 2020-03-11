---
title: 'Langage de requête Azure Cosmos DB : LENGTH'
description: Découvrez la fonction système SQL LENGTH dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303713"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
 Retourne le nombre de caractères de l’expression de chaîne spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est l’expression de chaîne à évaluer.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la longueur d’une chaîne.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
