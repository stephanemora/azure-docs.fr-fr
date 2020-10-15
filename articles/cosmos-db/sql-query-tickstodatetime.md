---
title: 'Langage de requête Azure Cosmos DB : TicksToDateTime'
description: Découvrez la fonction système SQL TicksToDateTime dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 89a8dba97725049b86fc6b38c09e0dd125bb48d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608630"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)

Convertit la valeur de cycles spécifiée en DateHeure.
  
## <a name="syntax"></a>Syntaxe
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Arguments

*Cycles*  

Valeur numérique signée indiquant le nombre actuel de cycles de 100 nanosecondes qui se sont écoulés depuis l’époque UNIX. En d’autres termes, il s’agit du nombre de cycles de 100 nanosecondes qui se sont écoulés depuis le jeudi 1er janvier 1970 à 00:00:00.

## <a name="return-types"></a>Types de retour

Retourne une valeur de chaîne ISO 8601 de date et heure UTC au format `YYYY-MM-DDThh:mm:ss.fffffffZ` où :
  
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

## <a name="remarks"></a>Notes

TicksToDateTime retourne `undefined` si la valeur de cycles spécifiée n’est pas valide.

## <a name="examples"></a>Exemples
  
L’exemple suivant convertit les cycles en DateHeure :

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de date et heure Azure Cosmos DB](sql-query-date-time-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
