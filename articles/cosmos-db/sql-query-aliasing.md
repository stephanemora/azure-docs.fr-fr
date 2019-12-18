---
title: Attribution d’alias dans Azure Cosmos DB
description: Découvrez comment utiliser les alias dans des requêtes SQL Azure Cosmos DB pour différencier deux propriétés portant le même nom
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873469"
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
