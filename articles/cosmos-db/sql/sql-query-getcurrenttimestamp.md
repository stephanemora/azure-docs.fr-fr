---
title: 'Langage de requête Azure Cosmos DB : GetCurrentTimestamp'
description: Découvrez la fonction système SQL GetCurrentTimestamp dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4ddb01175f0affad4e2ed3c441e7fa3919b6f174
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563532"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne le nombre de millisecondes qui se sont écoulées depuis le jeudi 1er janvier 1970 à 00:00:00.
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Types de retour
  
Retourne une valeur numérique signée représentant le nombre de millisecondes qui se sont écoulées depuis l’époque Unix, c’est-à-dire, depuis le jeudi 1er janvier 1970 à 00:00:00.

## <a name="remarks"></a>Notes

GetCurrentTimestamp() est une fonction non déterministe. Le résultat retourné est au format UTC (temps universel coordonné).

> [!NOTE]
> Cette fonction système n’utilisera pas l’index. Si vous devez comparer des valeurs à l’heure actuelle, obtenez l’heure actuelle avant d’exécuter la requête et utilisez cette valeur de chaîne constante dans la clause `WHERE`.

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
- [Présentation d’Azure Cosmos DB](../introduction.md)
