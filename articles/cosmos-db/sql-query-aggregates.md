---
title: Fonctions d’agrégation dans Azure Cosmos DB
description: Découvrez la syntaxe des fonctions d’agrégation SQL pour Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343171"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Fonctions d’agrégation dans Azure Cosmos DB

Les fonctions d’agrégation effectuent un calcul sur un ensemble de valeurs dans la clause SELECT et retournent une valeur unique. Par exemple, la requête suivante retourne le nombre d’éléments présents dans le conteneur `Families` :

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

L’API SQL prend en charge les fonctions d’agrégation suivantes. SUM et AVG opèrent sur des valeurs numériques, tandis que COUNT, MIN et MAX fonctionnent sur des nombres, des chaînes, des valeurs booléennes et des valeurs Null.

| Fonction | Description |
|-------|-------------|
| COUNT | Renvoie le nombre d’éléments que contient l’expression. |
| SUM   | Renvoie la somme de toutes les valeurs de l’expression. |
| MIN   | Renvoie la valeur minimale de l’expression. |
| MAX   | Renvoie la valeur maximale de l’expression. |
| MOY   | Renvoie la moyenne des valeurs de l’expression. |

Vous pouvez également effectuer des agrégations sur les résultats d’une itération de tableau.

> [!NOTE]
> Dans l’Explorateur de données du portail Azure, les requêtes d’agrégation peuvent agréger des résultats partiels sur une seule page de requête. Le SDK génère une valeur cumulée unique sur toutes les pages. Pour effectuer des requêtes d’agrégation à l’aide de code, vous avez besoin du SDK .NET 1.12.0, du SDK .NET Core 1.1.0 ou du SDK Java 1.9.5 ou ultérieur.

## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’Azure Cosmos DB](introduction.md)
- [Fonctions système](sql-query-system-functions.md)
- [Fonctions définies par l’utilisateur](sql-query-udfs.md)