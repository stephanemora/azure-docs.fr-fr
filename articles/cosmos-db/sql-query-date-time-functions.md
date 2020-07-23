---
title: 'Langage de requête Azure Cosmos DB : fonctions de date et heure'
description: Découvrez les fonctions système SQL de date et d’heure dans Azure Cosmos DB pour effectuer des opérations Date/Heure et timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246935"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Fonctions de date et heure (Azure Cosmos DB)

Les fonctions de date et heure vous permettent d’effectuer des opérations d’horodatage et DateHeure dans Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Fonctions permettant d'obtenir la date et l'heure

Les fonctions scalaires suivantes vous permettent d'obtenir la date et l'heure UTC actuelles sous deux formes : une chaîne conforme au format ISO 8601 ou un horodatage numérique dont la valeur est l'époque Unix, en millisecondes.

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>Fonctions à utiliser avec les valeurs DateTime

Les fonctions suivantes vous permettent de manipuler facilement les valeurs DateTime :

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
- [Fonctions définies par l’utilisateur](sql-query-udfs.md)
- [Agrégats](sql-query-aggregates.md)
