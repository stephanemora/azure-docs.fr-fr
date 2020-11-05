---
title: Fonctions d’agrégation dans Azure Cosmos DB
description: Découvrez la syntaxe de la fonction d’agrégation SQL et les types de fonctions d’agrégation prises en charge par Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 7c988f379e94bf2f69854c90d45af42fe2a7ec4f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332778"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Fonctions d’agrégation dans Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Les fonctions d’agrégation effectuent un calcul sur un ensemble de valeurs dans la clause `SELECT` et retournent une valeur unique. Par exemple, la requête suivante retourne le nombre d’éléments présents dans le conteneur `Families` :

## <a name="examples"></a>Exemples

```sql
    SELECT COUNT(1)
    FROM Families f
```

Les résultats sont :

```json
    [{
        "$1": 2
    }]
```

Vous pouvez également ne retourner que la valeur scalaire de l’agrégation à l’aide du mot clé VALUE. Par exemple, la requête suivante renvoie le nombre de valeurs sous forme de nombre unique :

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Les résultats sont :

```json
    [ 2 ]
```

Vous pouvez également combiner des agrégations avec des filtres. Par exemple, la requête suivante retourne le nombre d’éléments dont l’État de l’adresse est `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Les résultats sont :

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Types de fonctions d’agrégation

L’API SQL prend en charge les fonctions d’agrégation suivantes. `SUM` et `AVG` opèrent sur des valeurs numériques, tandis que `COUNT`, `MIN` et `MAX` fonctionnent sur des nombres, des chaînes, des booléens et des valeurs Null.

| Fonction | Description |
|-------|-------------|
| COUNT | Renvoie le nombre d’éléments que contient l’expression. |
| SUM   | Renvoie la somme de toutes les valeurs de l’expression. |
| MIN   | Retourne la valeur minimale de l'expression. |
| MAX   | Retourne la valeur maximale de l'expression. |
| AVG   | Renvoie la moyenne des valeurs de l’expression. |

Vous pouvez également effectuer des agrégations sur les résultats d’une itération de tableau.

> [!NOTE]
> Dans l’Explorateur de données du portail Azure, les requêtes d’agrégation peuvent agréger des résultats partiels sur une seule page de requête. Le kit SDK génère une valeur cumulée unique sur toutes les pages. Pour effectuer des requêtes d’agrégation à l’aide de code, vous avez besoin du kit SDK .NET 1.12.0, du kit SDK .NET Core 1.1.0 ou du kit SDK Java 1.9.5 ou ultérieur.

## <a name="remarks"></a>Notes

Ces fonctions système d’agrégation bénéficieront d’un [index de plage](index-policy.md#includeexclude-strategy). Si vous comptez exécuter une fonction `COUNT`, `SUM`, `MIN`, `MAX` ou `AVG` sur une propriété, vous devez [inclure le chemin d’accès correct dans la stratégie d’indexation](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’Azure Cosmos DB](introduction.md)
- [Fonctions système](sql-query-system-functions.md)
- [Fonctions définies par l’utilisateur](sql-query-udfs.md)