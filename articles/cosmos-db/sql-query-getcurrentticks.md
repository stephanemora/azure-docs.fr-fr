---
title: 'Langage de requête Azure Cosmos DB : GetCurrentTicks'
description: Découvrez la fonction système SQL GetCurrentTicks dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2ca76d75edba6688dbe93f11a51a0ad67942677a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606935"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

Retourne le nombre de cycles de 100 nanosecondes qui se sont écoulés depuis le jeudi 1er janvier 1970 à 00:00:00.
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Types de retour

Retourne une valeur numérique signée indiquant le nombre actuel de cycles de 100 nanosecondes qui se sont écoulés depuis l’époque UNIX. En d’autres termes, GetCurrentTicks retourne le nombre de cycles de 100 nanosecondes qui se sont écoulés depuis le jeudi 1er janvier 1970 à 00:00:00.

## <a name="remarks"></a>Notes

GetCurrentTicks() est une fonction non déterministe. Le résultat retourné est au format UTC (temps universel coordonné).

Cette fonction système n’utilisera pas l’index.

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
- [Présentation d’Azure Cosmos DB](introduction.md)
