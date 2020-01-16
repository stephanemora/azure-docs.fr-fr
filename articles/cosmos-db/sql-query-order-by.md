---
title: Clause ORDER BY dans Azure Cosmos DB
description: Découvrez la clause SQL ORDER BY pour Azure Cosmos DB. Utilisez SQL comme langage de requête JSON Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 5cae2bdd7d1f2f26e626c81ea95d2cee3cc8ae13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444794"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Clause ORDER BY dans Azure Cosmos DB

La clause ORDER BY facultative spécifie l’ordre de tri des résultats retournés par la requête.

## <a name="syntax"></a>Syntaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Arguments
  
- `<sort_specification>`  
  
   Spécifie une propriété ou expression sur laquelle trier le jeu de résultats de requête. Une colonne de tri peut être spécifiée en tant qu’alias de nom ou de propriété.  
  
   Plusieurs propriétés peuvent être spécifiées. Les noms de propriété doivent être uniques. La séquence des propriétés de tri dans la clause ORDER BY détermine l’organisation du jeu de résultats trié. Autrement dit, le jeu de résultats est trié par la première propriété, puis cette liste triée est triée par la deuxième propriété, et ainsi de suite.  
  
   Les noms de propriétés référencés dans la clause ORDER BY doivent correspondre à une propriété dans la liste de sélection ou à une propriété définie dans la collection spécifiée dans la clause FROM sans ambiguïté.  
  
- `<sort_expression>`  
  
   Spécifie une ou plusieurs propriétés ou expressions sur lesquelles trier le jeu de résultats de requête.  
  
- `<scalar_expression>`  
  
   Consultez la section [Expressions scalaires](sql-query-scalar-expressions.md) pour plus d’informations.  
  
- `ASC | DESC`  
  
   Spécifie que les valeurs dans la colonne spécifiée doivent être triées par ordre croissant ou décroissant. ASC effectue le tri de la valeur la plus faible à la valeur la plus élevée. DESC effectue le tri de la valeur la plus élevée à la valeur la plus faible. ASC correspond à l'ordre de tri par défaut. Les valeurs NULL sont traitées comme les plus petites valeurs possibles.  
  
## <a name="remarks"></a>Notes  
  
   La clause ORDER BY nécessite que la stratégie d’indexation comprenne un index pour les champs de tri. Le runtime de requête Azure Cosmos DB prend en charge le tri par rapport à un nom de propriété et non par rapport à des propriétés calculées. Azure Cosmos DB prend en charge plusieurs propriétés ORDER BY. Pour exécuter une requête avec plusieurs propriétés ORDER BY, vous devez définir un [index composite](index-policy.md#composite-indexes) sur les champs de tri.
   
> [!Note] 
> Si vous utilisez le kit SDK .NET 3.4.0 ou ultérieur, si les propriétés en cours de tri peuvent ne pas être définies pour certains documents, vous devez créer explicitement un index sur ces propriétés. La stratégie d’indexation par défaut n’autorise pas la récupération des documents où la propriété de tri n’est pas définie.

## <a name="examples"></a>Exemples

Par exemple, voici une requête qui récupère les familles dans l’ordre croissant de la ville de résidence :

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Les résultats sont :

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

La requête suivante récupère les `id` de famille dans l’ordre de la date de création de leur élément. L’élément `creationDate` est un nombre représentant l’*heure d’époque*, ou le temps écoulé depuis le 1er janvier 1970 en secondes.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Les résultats sont :

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

De plus, vous pouvez trier par plusieurs propriétés. Une requête qui trie par plusieurs propriétés requiert un [index composite](index-policy.md#composite-indexes). Considérez la requête suivante :

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Cette requête récupère les `id` de famille dans l’ordre croissant du nom de la ville. Si plusieurs éléments ont le même nom de ville, la requête trie par la `creationDate` dans l’ordre décroissant.

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer](sql-query-getting-started.md)
- [Clause SELECT](sql-query-select.md)
- [Clause OFFSET LIMIT](sql-query-offset-limit.md)
