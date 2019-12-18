---
title: Requêtes paramétrables dans Azure Cosmos DB
description: Découvrez dans quelle mesure les requêtes SQL paramétrées fournissent une gestion et un échappement robustes de l’entrée utilisateur et empêchent l’exposition accidentelle des données via l’injection SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870817"
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

Les valeurs de paramètres peuvent être n’importe quel format JSON valide : chaînes, nombres, booléens, Null, même des tableaux ou des valeurs JSON imbriquées. Cosmos DB étant sans schéma, les paramètres ne sont pas validés par rapport à un type.


## <a name="next-steps"></a>Étapes suivantes

- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modéliser des données de document](modeling-data.md)
