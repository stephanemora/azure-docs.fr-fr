---
title: Opérateurs de requête SQL pour Azure Cosmos DB
description: Découvrez les opérateurs SQL, tels que les opérateurs d’égalité, de comparaison et logiques pris en charge par Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870936"
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

## <a name="-and--operators"></a>? et ?? , opérateurs

Vous pouvez utiliser les opérateurs Ternary (?) et Coalesce (??) pour créer des expressions conditionnelles, comme dans des langages de programmation courants tels que C# et JavaScript. 

Vous pouvez utiliser l’opérateur ? pour construire de nouvelles propriétés JSON à la volée. Par exemple, la requête suivante classifie les niveaux de classe dans `elementary` ou `other` :

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

Utilisez l’opérateur ?? pour vérifier efficacement la présence d’une propriété dans un élément quand l’interrogation porte sur des données semi-structurées ou de type mixte. Par exemple, la requête suivante retourne `lastName` s’il est présent, ou `surname` si `lastName` n’est pas présent.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Étapes suivantes

- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Mots clés](sql-query-keywords.md)
- [Clause SELECT](sql-query-select.md)
