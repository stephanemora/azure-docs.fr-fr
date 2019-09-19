---
title: Attribution d’alias dans Azure Cosmos DB
description: Découvrez l’attribution d’alias à des valeurs dans les requêtes SQL Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002083"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Attribution d’alias dans Azure Cosmos DB

Vous pouvez explicitement attribuer des alias à des valeurs dans les requêtes. Si une requête a deux propriétés portant le même nom, utilisez l’attribution d’alias pour renommer l’une ou l’autre des propriétés, afin d’éviter toute ambiguïté dans le résultat projeté.

## <a name="examples"></a>Exemples

Le mot clé AS utilisé pour l’attribution d’alias est facultatif, comme le montre l’exemple suivant, lors de la projection de la deuxième valeur en tant que `NameInfo` :

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Étapes suivantes

- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clause SELECT](sql-query-select.md)
- [Clause FROM](sql-query-from.md)
