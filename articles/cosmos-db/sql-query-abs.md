---
title: 'Langage de requête Azure Cosmos DB : ABS'
description: En savoir plus sur la façon dont la fonction système SQL absolu (ABS) dans Azure Cosmos DB retourne la valeur positive de l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70648dcf28d760c3a81c0cb426cd9b2d3ce96fff
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871854"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 Retourne la valeur (positive) absolue de l'expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre les résultats de l’utilisation de la fonction `ABS` sur trois nombres différents.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
