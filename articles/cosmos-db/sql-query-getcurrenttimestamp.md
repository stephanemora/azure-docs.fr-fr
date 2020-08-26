---
title: 'Langage de requête Azure Cosmos DB : GetCurrentTimestamp'
description: Découvrez la fonction système SQL GetCurrentTimestamp dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1e0a8d69edab0c01005268ee49c23625236f03a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606925"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)

 Retourne le nombre de millisecondes qui se sont écoulées depuis le jeudi 1er janvier 1970 à 00:00:00.
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Types de retour
  
Retourne une valeur numérique signée représentant le nombre de millisecondes qui se sont écoulées depuis l’époque Unix, c’est-à-dire, depuis le jeudi 1er janvier 1970 à 00:00:00.

## <a name="remarks"></a>Notes

GetCurrentTimestamp() est une fonction non déterministe. Le résultat retourné est au format UTC (temps universel coordonné).

Cette fonction système n’utilisera pas l’index.

## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment obtenir l’horodatage actuel à l’aide de la fonction intégrée GetCurrentTimestamp().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Voici un exemple de jeu de résultats.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de date et heure Azure Cosmos DB](sql-query-date-time-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
