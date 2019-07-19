---
title: Requêtes paramétrables dans Azure Cosmos DB
description: En savoir plus sur les requêtes paramétrables SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343113"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Requêtes paramétrables dans Azure Cosmos DB

Cosmos DB prend en charge les requêtes avec des paramètres exprimées par la notation @ classique. SQL paramétré fournit une gestion et un échappement robustes de l’entrée utilisateur et empêche l’exposition accidentelle des données par l’intermédiaire de l’injection SQL.

## <a name="examples"></a>Exemples

Par exemple, vous pouvez écrire une requête qui accepte `lastName` et `address.state` comme paramètres et l’exécuter pour différentes valeurs de `lastName` et `address.state` basées sur l’entrée d’utilisateur.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Vous pouvez ensuite envoyer cette demande à Cosmos DB sous la forme d’une requête JSON paramétrable comme suit :

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

L’exemple suivant définit l’argument TOP avec une requête paramétrable : 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Les valeurs de paramètres peuvent être n’importe quel format JSON valide : chaînes, nombres, valeurs booléennes, Null, même des tableaux ou des valeurs JSON imbriquées. Cosmos DB étant sans schéma, les paramètres ne sont pas validés par rapport à un type.


## <a name="next-steps"></a>Étapes suivantes

- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modéliser des données de document](modeling-data.md)
