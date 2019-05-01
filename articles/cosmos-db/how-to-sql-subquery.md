---
title: Sous-requêtes SQL pour Azure Cosmos DB
description: En savoir plus sur les sous-requêtes SQL et leurs cas d’utilisation courants dans Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514798"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exemples de sous-requête SQL pour Azure Cosmos DB

Une sous-requête est une requête imbriquée dans une autre requête. Une sous-requête est également appelée requête interne ou sélection interne et l’instruction contenant une sous-requête est généralement appelée une requête externe.

Il existe deux types de sous-requêtes :

* Corrélées - une sous-requête corrélées est une sous-requête qui fait référence à valeurs de la requête externe. La sous-requête est évaluée une seule fois pour chaque ligne qui est traité par la requête externe.

* Non liées à une sous-requête corrélée Non A est une sous-requête qui est indépendante de la requête externe, et elle peut être exécutée sur son propre sans dépendre de la requête externe.

> [!NOTE]
> Azure Cosmos DB prend en charge les sous-requêtes en corrélation.

## <a name="types-of-subqueries"></a>Types des sous-requêtes

Les sous-requêtes peuvent être davantage classés en fonction du nombre de lignes et colonnes qu’elles retournent. Il existe trois types différents :
1.  **Table**: Retourne plusieurs lignes et les colonnes multiples
2.  **Valeurs multiples**: Retourne plusieurs lignes et une seule colonne
3.  **Scalaire**: Retourne une seule ligne et une seule colonne

> [!NOTE]
> Azure Cosmos DB prend en charge les sous-requêtes à valeurs multiples et scalaires

Les requêtes SQL Cosmos DB Azure retournent toujours une seule colonne (une valeur simple ou un document complexe). Par conséquent, uniquement les sous-requêtes à valeurs multiples et scalaire ci-dessus sont applicables dans Azure Cosmos DB. Une sous-requête de valeur multiple utilisable uniquement dans la clause FROM comme une expression relationnelle, si une sous-requête scalaire peut être utilisée comme une expression scalaire dans l’instruction SELECT ou une clause WHERE ou comme une expression relationnelle dans la clause FROM.


## <a name="multi-value-subqueries"></a>Sous-requêtes à valeurs multiples

Les sous-requêtes à valeurs multiples retournent un ensemble de documents et sont toujours utilisés dans la clause FROM. Ils sont utilisés pour :

* Optimisation des expressions de jointure 
* L’évaluation d’expressions coûteuses qu’une seule fois et en faisant référence à plusieurs fois

### <a name="optimize-join-expressions"></a>Optimiser les expressions de jointure

Les sous-requêtes à valeurs multiples peuvent optimiser des expressions de jointure en envoyant des prédicats après chaque expression select-plusieurs, plutôt qu’après toutes les jointures croisées dans la clause WHERE.

Examinez la requête suivante :

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Pour cette requête, l’index correspond tout document qui a une balise avec la nom 'naissantes formule », un élément nutritif avec une valeur comprise entre 0 et 10 et un élément de service avec un montant supérieur à 1. Toutefois, l’expression de jointure effectue le produit croisé de tous les éléments de tableaux de balises, les éléments et les portions pour chaque document correspondant avant que n’importe quel filtre est appliqué. La clause WHERE s’applique ensuite le tuple de prédicat sur chaque < c, t, n, s > filtre. Par exemple, si un document correspondant comporte 10 éléments dans chacun des trois tableaux, il développe et 1 x 10 x 10 x 10 (par exemple, 1 000) tuples. Sous-requêtes ici, peuvent aider à filtrant des éléments de tableau joint avant une jointure avec l’expression suivante.

Cette requête est équivalente à celle ci-dessus, mais utilise des sous-requêtes :

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

En supposant qu’un seul élément du tableau de balises correspond au filtre et cinq éléments pour les éléments et les portions tableaux, les expressions de jointure seront étendra à 1 x 1 x 5 x 5 = 25 éléments par opposition à 1 000 éléments dans la première requête.

### <a name="evaluate-once-and-reference-many-times"></a>Évaluer une seule fois et référence plusieurs fois

Les sous-requêtes peuvent aider à optimiser les requêtes avec des expressions coûteuses telles que les fonctions définies par l’utilisateur (UDF) ou de chaîne complexe ou des expressions arithmétiques. Vous pouvez utiliser une sous-requête avec une expression de jointure pour évaluer l’expression en une seule fois mais elle référence plusieurs fois.

La requête suivante exécute l’UDF GetMaxNutritionValue à deux reprises :

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Voici une requête équivalente qui s’exécute uniquement l’UDF qu’une seule fois :

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Étant donné le comportement du produit croisé d’expressions de jointure, si l’expression de fonction UDF peut correspondre à non défini, vous devez vous assurer que l’expression de jointure toujours produit une seule ligne en retournant un objet à partir de la sous-requête plutôt que la valeur directement.
>

Voici un exemple similaire qui retourne un objet, plutôt qu’une valeur :

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

L’approche n’est pas limité à ces fonctions, mais au lieu de cela, à n’importe quelle expression potentiellement coûteuse. Par exemple, que nous pouvions accepter la même approche avec la fonction mathématique avg :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Imiter la jointure avec les données de référence externe

Souvent, nous devons faire référence à des données statiques qui changent rarement, telles que des mesures ou des codes de pays. Pour ces données, il est préférable de ne pas en double pour chaque document. Comment éviter cette duplication pour enregistrer sur le stockage et améliorer les performances d’écriture en conservant la taille des documents plus petits. Une sous-requête peut être utilisée ici pour imiter la sémantique de jointure interne avec une collection de données de référence.
Par exemple, prenons ce jeu de données de référence.

| **Unité** | **Name**            | **Multiplicateur** | **Unité de base** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Ordre du nanogramme            | 1.00E-09       | Gramme          |
| µg       | Microgramme           | 1.00E-06       | Gramme          |
| mg       | Mg           | 1.00E-03       | Gramme          |
| s        | Gramme                | 1.00E+00       | Gramme          |
| kg       | Kilogramme            | 1.00E + 03       | Gramme          |
| groupe d’administration       | Megagram            | 1.00E + 06       | Gramme          |
| GG       | Gigagram            | 1.00E + 09       | Gramme          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| kJ       | Kilojoule           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| licence d’accès client      | Calories             | 1.00E+00       | calories       |
| kcal     | Calories             | 1.00E + 03       | calories       |
| IU       | Unités internationales |                |               |


La requête suivante reproduit la jointure avec ces données afin que nous ajoutons le nom de l’unité à la sortie :

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Sous-requêtes scalaires

Une expression de sous-requête scalaire est une sous-requête qui prend une valeur unique. La valeur de l’expression de sous-requête scalaire est la valeur de la projection (clause SELECT) de la sous-requête.  Une expression de sous-requête scalaire peut être utilisée dans de nombreux endroits une expression scalaire est valide. Par exemple, une sous-requête scalaire peut être utilisée de toute expression dans les deux la sélectionner et de clauses WHERE.
Toutefois, à l’aide d’une sous-requête scalaire ne pas toujours aider à optimiser. Par exemple, en passant une sous-requête scalaire en tant qu’argument à un système ou de fonctions définies par l’utilisateur n’offre aucun avantage dans la consommation d’unités de requête ou la latence.

Sous-requêtes scalaires peuvent être davantage classés en tant que :
* Expression simple des sous-requêtes scalaires
* Agrégation sous-requêtes scalaires

### <a name="simple-expression-scalar-subqueries"></a>Expression simple des sous-requêtes scalaires

Une sous-requête scalaire simple-expression est une sous-requête corrélée qui a une clause SELECT qui ne contient-elle pas les expressions d’agrégation. Ces sous-requêtes n’apporte aucun avantage de l’optimisation, car le compilateur les convertit en une plus grande expression simple. Il n’y a aucun contexte de corrélation entre la requête interne et externe.

Voici quelques exemples :

**Exemple 1**

```sql
SELECT 1 AS a, 2 AS b
```

Cette requête pourrait être réécrit à l’aide d’une sous-requête scalaire simple-expression pour :

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Les deux requêtes génère cette sortie :

```json
[
  { "a": 1, "b": 2 }
]
```

**Exemple 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Cette requête pourrait être réécrit à l’aide d’une sous-requête scalaire simple-expression pour :

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Résultat de la requête :

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Exemple 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Cette requête pourrait être réécrit à l’aide d’une sous-requête scalaire simple-expression pour :

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Résultat de la requête :

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

## <a name="aggregate-scalar-subqueries"></a>Agrégation sous-requêtes scalaires

Une sous-requête scalaire agrégation est une sous-requête qui a une fonction d’agrégation dans sa projection ou un filtre qui correspond à une valeur unique.

**Exemple 1 :**

Voici une sous-requête avec une expression de fonction d’agrégation unique dans sa projection :

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Résultat de la requête :

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Exemple 2**

Une sous-requête avec plusieurs expressions de fonction d’agrégation :

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Résultat de la requête :

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Exemple 3**

Une requête avec une sous-requête agrégation dans la projection et le filtre :

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Résultat de la requête :

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Une solution plus optimale pour écrire cette requête consiste à joindre dans la sous-requête et référencer la sous-requête alias dans les deux l’instruction SELECT et des clauses WHERE. Cette requête est plus efficace, car nous devons exécuter la sous-requête uniquement au sein de l’instruction de jointure et non dans la projection et le filtre.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>Expression EXISTS

Azure Cosmos DB prend en charge les expressions EXISTS. Il s’agit d’une sous-requête scalaire agrégation intégrée à l’API SQL Azure Cosmos DB. EXISTS est une expression booléenne qui prend une expression de sous-requête et renvoie true si la sous-requête retourne toutes les lignes ; Sinon, elle retourne false.
Étant donné que l’API SQL Azure Cosmos DB ne différencie pas les expressions booléennes et toutes les expressions scalaires, EXISTS peut être utilisé dans les deux, sélectionnez et les clauses WHERE. Cela diffère de T-SQL, où une expression booléenne (par exemple, EXISTS, BETWEEN et IN) est limitée au filtre.

Si la sous-requête EXISTS retourne une valeur unique qui n’est pas définie, alors EXISTS est évalué à false. Par exemple, considérez la requête suivante qui a la valeur false :
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Toutefois, si le mot clé VALUE dans la sous-requête ci-dessus est omis la requête est évaluée comme vraie :
```sql
SELECT EXISTS (SELECT undefined) 
```

La sous-requête inclura la liste des valeurs dans la liste de sélection dans un objet. Si la liste sélectionnée n’a aucune valeur, la sous-requête retourne la valeur unique '{}' qui est défini et il n’existe donc a la valeur true.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Exemple : Réécriture ARRAY_CONTAINS et jointure de EXISTS

Un cas d’usage courant de ARRAY_CONTAINS consiste à filtrer un document par l’existence d’un élément dans un tableau. Dans ce cas, nous vérifions si le tableau tags contient une élément nommée orange.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

La même requête pourrait être réécrit pour utiliser EXISTS :

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

En outre, ARRAY_CONTAINS n’est en mesure de vérifier si une valeur est égale à n’importe quel élément dans un tableau. Si des filtres plus complexes sont nécessaires sur les propriétés du tableau, une jointure est requise.

Considérez la requête suivante que les filtres basés sur les unités et nutritionValue propriétés dans le tableau : 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Pour chacun des documents dans la collection, un produit croisé est effectué avec ses éléments de tableau. Cette opération de jointure permet de filtrer sur les propriétés dans le tableau. Toutefois, la consommation de RU de cette requête sera importante. Par exemple, si 1 000 documents avaient 100 éléments de chaque tableau, il va se développer à 1 000 x 100 (par exemple, 100 000) tuples.

À l’aide de `EXISTS` peut aider à éviter ce produit croisé cher :

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Dans ce cas, nous allons filtrer sur des éléments de tableau dans la sous-requête EXISTS. Si un élément de tableau correspond au filtre, puis nous le projet et `EXISTS` a la valeur true.

Nous pouvons également alias EXISTS et référencez-le dans la projection :

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Résultat de la requête :

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

### <a name="array-expression"></a>Expression de tableau

Le `ARRAY` expression peut être utilisée pour projeter les résultats d’une requête sous forme de tableau. Cette expression peut être utilisée uniquement dans la clause SELECT de la requête.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Résultat de la requête :

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Comme avec d’autres sous-requêtes, de filtres avec le `ARRAY` expression sont possibles.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Résultat de la requête :

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Expressions de tableau peuvent également provenir après la clause FROM dans les sous-requêtes.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Résultat de la requête :

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de requêtes SQL](how-to-sql-query.md)
- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Données de Document de modèle](modeling-data.md)
