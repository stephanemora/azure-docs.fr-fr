---
title: 'Langage de requête Azure Cosmos DB : GetCurrentTicks'
description: Découvrez la fonction système SQL GetCurrentTicks dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b99f15d3c72bd3bf3287276dcbd1838ef0257c20
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563552"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Retourne le nombre de cycles de 100 nanosecondes qui se sont écoulés depuis le jeudi 1er janvier 1970 à 00:00:00.
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Types de retour

Retourne une valeur numérique signée indiquant le nombre actuel de cycles de 100 nanosecondes qui se sont écoulés depuis l’époque UNIX. En d’autres termes, GetCurrentTicks retourne le nombre de cycles de 100 nanosecondes qui se sont écoulés depuis le jeudi 1er janvier 1970 à 00:00:00.

## <a name="remarks"></a>Notes

GetCurrentTicks() est une fonction non déterministe. Le résultat retourné est au format UTC (temps universel coordonné).

> [!NOTE]
> Cette fonction système n’utilisera pas l’index. Si vous devez comparer des valeurs à l’heure actuelle, obtenez l’heure actuelle avant d’exécuter la requête et utilisez cette valeur de chaîne constante dans la clause `WHERE`.

## <a name="examples"></a>Exemples

Voici un exemple qui retourne l’heure actuelle, exprimée en nombre de cycles :

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de date et heure Azure Cosmos DB](sql-query-date-time-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
