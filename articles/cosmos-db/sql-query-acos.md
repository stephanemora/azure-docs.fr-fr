---
title: 'Langage de requête Azure Cosmos DB : ACOS'
description: Découvrez la fonction système SQL ACOS (arccosice) dans Azure Cosmos DB, qui retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873520"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelée arccosinus.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la valeur `ACOS` de -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
