---
title: 'Langage de requête Azure Cosmos DB : GetCurrentDateTime'
description: Découvrez la fonction système SQL GetCurrentDateTime dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b48237b5a7eb836c495612758eeb9eaa45029b26
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526583"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

> [!NOTE]
> Cette fonction système n’utilisera pas l’index. Si vous devez comparer des valeurs à l’heure actuelle, obtenez l’heure actuelle avant d’exécuter la requête et utilisez cette valeur de chaîne constante dans la clause `WHERE`.

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
