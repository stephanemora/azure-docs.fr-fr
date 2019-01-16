---
title: Types d'index dans Azure Cosmos DB
description: Présentation des types d'index dans Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 44fe262dc28a016af9eb01f28278b2c3d81d9034
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034082"
---
# <a name="index-types-in-azure-cosmos-db"></a>Types d'index dans Azure Cosmos DB

Plusieurs options s'offrent à vous lorsque vous configurez la stratégie d’indexation d'un chemin d'accès. Vous pouvez spécifier une ou plusieurs définitions d’indexation pour chaque chemin d’accès :

- **Type de données :** Chaîne, Nombre, Point, Polygone ou LineString (peut contenir une seule entrée par type de données par chemin).

- **Type d’index :** Hachage (requêtes d’égalité), Plage (requêtes d’égalité, de plage ou ORDER BY) ou Spatial (requêtes spatiales).

- **Précision :** Pour les index de hachage, cette valeur varie de 1 à 8 pour les chaînes et les nombres, et la valeur par défaut est 3. Pour un index de plage, la valeur de précision maximale est -1. Elle peut varier de 1 à 100 (précision maximale) pour les valeurs de chaîne ou numériques.

## <a name="index-kind"></a>Type d’index

Azure Cosmos DB prend en charge les types d’index de hachage et de plage pour chaque chemin d'accès qui peut être configuré pour le type de données Chaîne ou Nombre, ou les deux.

- **Index de hachage** prend en charge les requêtes d’égalité efficaces et JOIN. Dans la plupart des cas d’utilisation, les index de hachage ne nécessitent pas une précision plus élevée que la valeur par défaut de trois octets. Le type de données peut être Chaîne ou Nombre.

- **Index de plage**  prend en charge les requêtes d’égalité efficaces, les requêtes de plage (avec >, <, >=, <=, !=) et les requêtes ORDER BY. Par défaut, les requêtes ORDER BY nécessitent également une précision d’index maximale (-1). Le type de données peut être Chaîne ou Nombre.

- **Index spatial** prend en charge les requêtes spatiales efficaces (within et distance). Le type de données peut être Point, Polygone ou LineString. Azure Cosmos DB prend également en charge le type d’index spatial pour chaque chemin d'accès qui peut être spécifié pour les types de données Point, Polygone ou LineString. La valeur dans le chemin spécifié doit être un fragment GeoJSON valide, comme {"type": "Point", "coordinates": [0.0, 10.0]}. Azure Cosmos DB prend en charge l’indexation automatique des types de données Points, Polygone et LineString.

Voici quelques exemples de requêtes d'index de hachage, de portée et spatial à utiliser pour traiter :

| **Type d’index** | **Description/Cas d’utilisation** |
| ---------- | ---------------- |
| Hachage  | Le hachage disposant de l’élément /prop? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Le hachage disposant de l’élément /props/[]/? (ou / ou /props/) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Plage  | La plage disposant de l’élément /prop/? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop   |
| spatial     | La plage disposant de l’élément /prop/? (ou /) peut être utilisé pour traiter efficacement les requêtes suivantes :<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) -- avec indexation sur les points activée<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) -- avec indexation sur les polygones activée.     |

## <a name="default-behavior-of-index-kinds"></a>Comportement par défaut des types d’index

- S’il n’existe aucun index de plage (de n’importe quelle précision) pour signaler qu’une analyse peut être requise pour traiter la requête. Dans ce cas, par défaut, une erreur est renvoyée pour les requêtes avec des opérateurs de plage tels que >=.

- Les requêtes peuvent être effectuées sans index de plage à l’aide de l’en-tête "x-ms-documentdb-enable-scan" dans l’API REST ou l’option de requête "EnableScanInQuery" à l’aide du SDK .NET. Si d’autres filtres de la requête peuvent être utilisés par Azure Cosmos DB sur l’index, aucune erreur n’est retournée.

- Par défaut, une erreur est renvoyée pour les requêtes spatiales s’il n’existe aucun index spatial et qu’aucun autre filtre ne peut être fourni à partir de l’index. De telles requêtes peuvent être effectuées en tant qu’analyse à l’aide de x-ms-documentdb-enable-scan ou EnableScanInQuery.

## <a name="index-precision"></a>Précision d’index

- Vous pouvez utiliser la précision de l’index pour trouver un compromis entre le traitement du stockage de l’index et les performances des requêtes. Pour les nombres, nous recommandons d’utiliser la configuration de précision par défaut définie sur -1 (valeur maximale). Comme les nombres correspondent à huit octets dans JSON, cela équivaut à une configuration de huit octets. Si vous choisissez une valeur inférieure pour la précision, par exemple de un à sept, les valeurs de certaines plages sont mappées à la même entrée d’index. Vous pouvez ainsi réduire l’espace de stockage d’index, mais l’exécution des requêtes devra peut-être traiter davantage d'éléments. Elle consommera donc plus de débit/unités de requête.

- La précision d’index est plus pratique avec les plages de chaînes. Les chaînes pouvant avoir n’importe quelle longueur arbitraire, le choix de la précision d’index peut affecter les performances des requêtes de plage de chaînes. Il peut également affecter la quantité d’espace de stockage d’index nécessaire. Les index de plage de chaînes peuvent être configurés avec une précision d'index comprise entre 1 et 100, ou -1 (valeur maximale). Si vous souhaitez exécuter des requêtes ORDER BY sur des propriétés de chaîne, vous devez spécifier une précision de -1 pour les chemins correspondants.

- Les index spatiaux utilisent toujours la précision d’index par défaut pour tous les types (Point, LineString et Polygone). La précision d’index par défaut pour les index spatiaux ne peut pas être substituée.

Azure Cosmos DB retourne une erreur quand une requête utilise ORDER BY mais n’a pas d’index de plage pour le chemin avec la précision maximale.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l'indexation dans Azure Cosmos DB, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Stratégie d’indexation](indexing-policies.md)
- [Chemins d’accès de l’index](index-paths.md)

