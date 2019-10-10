---
title: 'Langage de requête Azure Cosmos DB : PI'
description: Découvrez la fonction système SQL PI dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349656"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Retourne la valeur constante de PI.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant retourne la valeur de `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
