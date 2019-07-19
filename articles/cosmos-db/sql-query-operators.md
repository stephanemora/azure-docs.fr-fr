---
title: Opérateurs de requête SQL pour Azure Cosmos DB
description: Découvrez les opérateurs SQL pour Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343255"
---
# <a name="operators-in-azure-cosmos-db"></a>Opérateurs dans Azure Cosmos DB

Cet article décrit les différents opérateurs pris en charge par Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Opérateurs d’égalité et de comparaison

Le tableau suivant répertorie les résultats des comparaisons d'égalité dans l’API SQL entre deux types JSON.

| **Opérateur** | **Undefined** | **Null** | **Booléen** | **Nombre** | **Chaîne** | **Object** | **Tableau** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Booléen** | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined |
| **Nombre** | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined |
| **Chaîne** | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined |
| **Tableau** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** |

Pour les opérateurs de comparaison tels que `>`, `>=`, `!=`, `<` et `<=`, la comparaison entre types ou entre deux objets ou tableaux génère `Undefined`.  

Si le résultat de l’expression scalaire est `Undefined`, l’élément n’est pas inclus dans le résultat, car `Undefined` n’est pas égal à `true`.

## <a name="logical-and-or-and-not-operators"></a>Opérateurs logiques (AND, OR et NOT)

Les opérateurs logiques interviennent sur des valeurs booléennes. Les tableaux suivants présentent les tables de vérité logiques de ces opérateurs :

**Opérateur OR**

| Ou | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**Opérateur OR**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**Opérateur NOT**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |


## <a name="-operator"></a>Opérateur *

L’opérateur spécial * projette l’élément entier en l’état. Une fois utilisé, il doit être le seul champ projeté. Une requête comme `SELECT * FROM Families f` est valide, contrairement à `SELECT VALUE * FROM Families f` et `SELECT *, f.id FROM Families f`.

## <a name="-and--operators"></a>Opérateurs ? et ??

Vous pouvez utiliser les opérateurs Ternary (?) et Coalesce (??) pour créer des expressions conditionnelles, comme dans des langages de programmation courants tels que C# et JavaScript. 

Vous pouvez utiliser l’opérateur ? pour construire de nouvelles propriétés JSON à la volée. Par exemple, la requête suivante classifie les niveaux de classe dans `elementary` ou `other` :

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Vous pouvez également imbriquer des appels à l’opérateur ? , comme dans la requête suivante : 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Comme avec d’autres opérateurs de requête, l’opérateur ? exclut des éléments si les propriétés référencées sont manquantes ou que les types comparés sont différents.

Utilisez l’opérateur ?? pour vérifier efficacement la présence d’une propriété dans un élément quand l’interrogation porte sur des données semi-structurées ou de type mixte. Par exemple, la requête suivante retourne `lastName` s’il est présent, ou `surname` si `lastName` n’est pas présent.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Étapes suivantes

- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Mots clés](sql-query-keywords.md)
- [Clause SELECT](sql-query-select.md)
