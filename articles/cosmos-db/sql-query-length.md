---
title: 'Langage de requête Azure Cosmos DB : LENGTH'
description: Découvrez la fonction système SQL LENGTH dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349757"
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

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
