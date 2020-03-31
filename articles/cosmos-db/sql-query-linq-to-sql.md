---
title: Conversion LINQ en SQL dans Azure Cosmos DB
description: Découvrez les opérateurs LINQ pris en charge et la façon dont les requêtes LINQ sont mappées aux requêtes SQL dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441254"
---
# <a name="linq-to-sql-translation"></a>Conversion LINQ en SQL

Le fournisseur de requêtes d’Azure Cosmos DB effectue le meilleur mappage possible entre une requête LINQ et une requête SQL Cosmos DB. La description suivante suppose une connaissance de base de LINQ.

Le système de type du fournisseur de requêtes prend en charge uniquement les types primitifs JSON : numérique, booléen, chaîne et null.

Le fournisseur de requêtes prend en charge les expressions scalaires suivantes :

- Valeurs constantes, y compris les valeurs constantes des types de données primitifs au moment de l’évaluation de la requête.
  
- Expressions d’index de propriété/tableau qui font référence à la propriété d’un objet ou d’un élément de tableau. Par exemple :
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressions arithmétiques, y compris les expressions arithmétiques communes sur les valeurs numériques et booléennes. Pour obtenir la liste complète, consultez la [spécification SQL Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressions de comparaison de chaîne, qui incluent les comparaisons de valeurs de chaînes à certaines valeurs de chaînes constantes.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressions de création d’objet/tableau, qui retournent un objet de type de valeur composée ou de type anonyme ou un tableau de tels objets. Vous pouvez imbriquer ces valeurs.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Opérateurs LINQ pris en charge

Le fournisseur LINQ inclus avec le kit SDK .NET SQL prend en charge les opérateurs suivants :

- **Select** : les projections sont converties en instructions SQL SELECT, y compris la construction d’objets.
- **Where** : les filtres sont convertis en instructions SQL WHERE et prennent en charge la conversion de `&&`, `||` et `!` en opérateurs SQL
- **SelectMany** : autorise le déroulement de tableaux vers la clause SQL JOIN. Permet d’associer/imbriquer des expressions afin de filtrer les éléments de tableau.
- **OrderBy** et **OrderByDescending** : sont convertis en ORDER BY avec ASC ou DESC.
- Les opérateurs **Count**, **Sum**, **Min**, **Max** et **Average** pour l’agrégation, et leurs équivalents asynchrones **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** et **AverageAsync**.
- **CompareTo** : se traduit par des comparaisons de plages. Généralement utilisés pour les chaînes, car elles ne sont pas comparables dans .NET.
- **Skip** et **Take** : traduit en SQL OFFSET et LIMIT afin de limiter les résultats d’une requête et d’effectuer la pagination.
- **Fonctions mathématiques** : prennent en charge la conversion des fonctions .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan` et `Truncate` vers les fonctions intégrées SQL équivalentes.
- **Fonctions de chaîne** : prennent en charge la conversion des fonctions .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd` et `TrimStart` vers les fonctions intégrées SQL équivalentes.
- **Fonctions de tableau** : prennent en charge la conversion des fonctions .NET `Concat`, `Contains` et `Count` vers les fonctions intégrées SQL équivalentes.
- **Fonctions d’extension géospatiale** : prennent en charge la conversion des méthodes stub `Distance`, `IsValid`, `IsValidDetailed` et `Within` vers les fonctions intégrées SQL équivalentes.
- **Fonction d’extension de fonction définie par l’utilisateur** : prend en charge la conversion de la méthode stub `UserDefinedFunctionProvider.Invoke` vers la fonction définie par l’utilisateur correspondante.
- **Miscellaneous** : prend en charge la conversion des opérateurs conditionnels et `Coalesce`. Peut convertir `Contains` en chaîne CONTAINS, ARRAY_CONTAINS ou SQL IN, selon le contexte.

## <a name="examples"></a>Exemples

Les exemples suivants illustrent la conversion de certains opérateurs de requête LINQ standard en requêtes Cosmos DB.

### <a name="select-operator"></a>Opérateur Select

La syntaxe est `input.Select(x => f(x))`, où `f` est une expression scalaire.

**Opérateur Select, exemple 1 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Opérateur Select, exemple 2 :** 

- **Expression Lambda LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Opérateur Select, exemple 3 :**

- **Expression Lambda LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Opérateur SelectMany

La syntaxe est `input.SelectMany(x => f(x))`, où `f` est une expression scalaire qui retourne un type de conteneur.

- **Expression Lambda LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Opérateur Where

La syntaxe est `input.Where(x => f(x))`, où `f` est une expression scalaire qui retourne une valeur booléenne.

**Opérateur Where, exemple 1 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Opérateur Where, exemple 2 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Requêtes SQL composites

Vous pouvez composer les opérateurs précédents pour former des requêtes plus puissantes. Étant donné que Cosmos DB prend en charge les conteneurs imbriqués, vous pouvez concaténer ou imbriquer la composition.

### <a name="concatenation"></a>Concaténation

La syntaxe est `input(.|.SelectMany())(.Select()|.Where())*`. Une requête concaténée peut commencer par une requête `SelectMany` facultative, suivie de plusieurs opérateurs `Select` ou `Where`.

**Concaténation, exemple 1 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Concaténation, exemple 2 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Concaténation, exemple 3 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Concaténation, exemple 4 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Imbrication

La syntaxe est `input.SelectMany(x=>x.Q())`, où `Q` est un opérateur `Select`, `SelectMany` ou `Where`.

Une requête imbriquée applique la requête interne à chaque élément du conteneur externe. Une caractéristique importante est la capacité de la requête interne à faire référence aux champs des éléments du conteneur externe, à l’instar d’une jointure réflexive.

**Imbrication, exemple 1 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Imbrication, exemple 2 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Imbrication, exemple 3 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Étapes suivantes

- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modéliser des données de document](modeling-data.md)
