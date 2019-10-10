---
title: 'Langage de requête Azure Cosmos DB : GetCurrentTimestamp'
description: Découvrez la fonction système SQL GetCurrentTimestamp dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351011"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Retourne le nombre de millisecondes qui se sont écoulées depuis le jeudi 1er janvier 1970 à 00:00:00. 
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une valeur numérique représentant le nombre de millisecondes qui se sont écoulées depuis l’époque Unix, c’est-à-dire, le nombre de millisecondes qui se sont écoulées depuis le jeudi 1er janvier 1970 à 00:00:00.

## <a name="remarks"></a>Remarques

  GetCurrentTimestamp() est une fonction non déterministe.
  
  Le résultat retourné est au format UTC (temps universel coordonné).

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
