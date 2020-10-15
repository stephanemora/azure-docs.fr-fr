---
title: 'Langage de requête Azure Cosmos DB : DateTimeToTimestamp'
description: Découvrez la fonction système SQL DateTimeToTimestamp dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 673e6a8cb86986ff24e3ee55589d13e8a9e4fdc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608627"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)

Convertit la valeur DateHeure spécifiée en horodatage.
  
## <a name="syntax"></a>Syntaxe
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Arguments

*DateTime*  
   Valeur de la chaîne ISO 8601 (date et heure UTC) au format `YYYY-MM-DDThh:mm:ss.fffffffZ` où :
  
  |Format|Description|
  |-|-|
  |YYYY|Année à quatre chiffres|
  |MM|Mois à deux chiffres (01 = janvier, etc.)|
  |DD|Jour du mois à deux chiffres (01 à 31)|
  |T|Indicateur de début des éléments de l’heure|
  |hh|Heure à deux chiffres (00 à 23)|
  |MM|Minutes à deux chiffres (00 à 59)|
  |ss|Secondes à deux chiffres (00 à 59)|
  |.fffffff|Fractions de seconde à 7 chiffres|
  |Z|Indicateur UTC (temps universel coordonné)||
  
  Pour plus d’informations sur le format ISO 8601, consultez [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601).

## <a name="return-types"></a>Types de retour

Retourne une valeur numérique signée représentant le nombre de millisecondes qui se sont écoulées depuis l’époque Unix, c’est-à-dire, depuis le jeudi 1er janvier 1970 à 00:00:00.

## <a name="remarks"></a>Notes

DateTimeToTimestamp retourne `undefined` si la valeur de DateHeure spécifiée n’est pas valide

## <a name="examples"></a>Exemples
  
L’exemple suivant convertit la valeur DateHeure en horodatage :

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Voici un autre exemple :

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de date et heure Azure Cosmos DB](sql-query-date-time-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
