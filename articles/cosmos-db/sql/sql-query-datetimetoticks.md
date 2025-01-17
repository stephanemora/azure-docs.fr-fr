---
title: 'Langage de requête Azure Cosmos DB : DateTimeToTicks'
description: Découvrez la fonction système SQL DateTimeToTicks dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: f732de3d886ac187b54e0890569aec3dcb302a53
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525835"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Convertit la valeur DateHeure spécifiée en cycles. Un cycle représente cent nanosecondes ou un dix-millionième de seconde. 

## <a name="syntax"></a>Syntaxe
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Arguments
  
*DateTime*  
   Valeur de la chaîne ISO 8601 (date et heure UTC) au format `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Types de retour

Retourne une valeur numérique signée, le nombre actuel de cycles de 100 nanosecondes qui se sont écoulés depuis l’époque UNIX. En d’autres termes, DateTimeToTicks retourne le nombre de cycles de 100 nanosecondes qui se sont écoulés depuis le jeudi 1er janvier 1970 00:00:00.

## <a name="remarks"></a>Notes

DateTimeToTicks retourne `undefined` si la valeur DateHeure n’est pas une DateHeure ISO 8601 valide

Cette fonction système n’utilisera pas l’index.

## <a name="examples"></a>Exemples

Voici un exemple qui retourne le nombre de cycles :

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Voici un exemple qui retourne le nombre de cycles sans spécifier le nombre de fractions de seconde :

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de date et heure Azure Cosmos DB](sql-query-date-time-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
