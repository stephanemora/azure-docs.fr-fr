---
title: 'Langage de requête Azure Cosmos DB : GetCurrentDateTime'
description: Découvrez la fonction système SQL GetCurrentDateTime dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ec0b8ccaceed4abe3dd2784463f507f3bc76d890
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606952"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)

Retourne la date et l’heure UTC actuelles sous forme de chaîne ISO 8601.
  
## <a name="syntax"></a>Syntaxe
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Types de retour
  
  Retourne la valeur de la chaîne ISO 8601 (date et heure UTC actuelles) au format `YYYY-MM-DDThh:mm:ss.fffffffZ` où :
  
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

GetCurrentDateTime() est une fonction non déterministe. Le résultat retourné est au format UTC. La précision est de 7 chiffres, avec une justesse de 100 nanosecondes.

Cette fonction système n’utilisera pas l’index.

## <a name="examples"></a>Exemples
  
L’exemple suivant montre comment obtenir la date et l’heure UTC actuelles à l’aide de la fonction intégrée GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Voici un exemple de jeu de résultats.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de date et heure Azure Cosmos DB](sql-query-date-time-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
