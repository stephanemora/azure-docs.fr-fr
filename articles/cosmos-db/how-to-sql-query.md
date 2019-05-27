---
title: Requêtes SQL pour Azure Cosmos DB
description: Obtenez plus d’informations sur la syntaxe SQL, les concepts de bases de données et les requêtes SQL pour Azure Cosmos DB. Utiliser SQL comme un langage de requête JSON Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: bbca0239053b8f3164055a07b376abc597b0348f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954135"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Exemples de requêtes SQL pour Azure Cosmos DB

Comptes d’API SQL Cosmos DB Azure prend en charge l’interrogation des éléments à l’aide du langage SQL (Structured Query) en tant que langage de requête JSON. Les objectifs de conception du langage de requête Azure Cosmos DB sont :

* Prend en charge SQL, parmi les langages de requête plus conviviaux et populaires, au lieu d’inventer un nouveau langage de requête. SQL fournit un modèle de programmation formel pour créer des requêtes élaborées sur les éléments JSON.  

* Utiliser le modèle de programmation de JavaScript comme base pour le langage de requête. Système de type de JavaScript, évaluation de l’expression et l’appel de fonction sont les racines de l’API SQL. Ces racines fournissent un modèle de programmation naturel pour les fonctionnalités telles que les projections relationnelles, une navigation hiérarchique entre les éléments JSON, des jointures réflexives, les requêtes spatiales et l’appel de fonctions définies par l’utilisateur (UDF) écrite entièrement en JavaScript.

Cet article vous montre quelques exemples de requêtes SQL sur des éléments JSON simples. Pour en savoir plus sur la syntaxe du langage SQL Azure Cosmos DB, consultez [référence de la syntaxe SQL](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Prise en main des requêtes SQL

Dans votre compte de l’API SQL Cosmos DB, créez un conteneur appelé `Families`. Créez deux éléments JSON simples dans le conteneur et exécuter quelques requêtes simples.

### <a name="create-json-items"></a>Créer des éléments de JSON

Le code suivant crée deux éléments JSON simples sur les familles. Les éléments JSON simples pour les familles Andersen et Wakefield incluent les parents, enfants et leurs animaux, adresse et informations d’inscription. Le premier élément a des chaînes, des chiffres, des valeurs booléennes, des tableaux et des propriétés imbriquées.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Le deuxième élément utilise `givenName` et `familyName` au lieu de `firstName` et `lastName`.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Interroger les éléments JSON

Essayez quelques requêtes sur les données JSON pour comprendre certains aspects clés du langage de requête SQL Azure Cosmos DB.

La requête suivante retourne les éléments où les `id` champ correspondances `AndersenFamily`. Dans la mesure où il est un `SELECT *` requête, la sortie de la requête est l’élément JSON complet. Pour plus d’informations sur la syntaxe SELECT, consultez [instruction SELECT](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats de requête sont : 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

La requête suivante remet en forme la sortie JSON dans une forme différente. La requête projette un nouveau modèle JSON `Family` objet avec 2 champs sélectionnés, `Name` et `City`, lorsque la ville de l’adresse est identique à l’état. « New York, NY » correspond à ce cas.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Les résultats de requête sont :

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

La requête suivante renvoie tous les noms donnés des enfants de la famille dont `id` correspond à `WakefieldFamily`, classée par ville.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Les résultats sont :

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Les exemples précédents décrivent plusieurs aspects du langage de requête Cosmos DB :  

* Dans la mesure où les API SQL fonctionne sur les valeurs JSON, il traite avec les entités en forme d’arborescence au lieu de lignes et colonnes. Vous pouvez consulter les nœuds d’arbre à n’importe quelle profondeur arbitraire, comme `Node1.Node2.Node3…..Nodem`, semblable à la référence de deux parties de `<table>.<column>` dans SQL ANSI.

* Étant donné que le langage de requête fonctionne avec des données sans schéma, le système de type doit être lié dynamiquement. La même expression peut produire différents types sur différents éléments. Le résultat d’une requête est une valeur JSON valide, mais n’est pas forcément un schéma fixe.  

* Azure Cosmos DB prend uniquement en charge les éléments JSON stricts. Le système de type et les expressions sont limitées uniquement traiter des types JSON. Pour plus d’informations, consultez le [spécification JSON](https://www.json.org/).  

* Un conteneur Cosmos DB est une collection sans schéma d’éléments JSON. Les relations au sein et entre les éléments de conteneur sont implicitement capturées par relation contenant-contenu, non par la clé primaire et les relations de clé étrangères. Cette fonctionnalité est importante pour les jointures intra-item décrits plus loin dans cet article.

## <a id="SelectClause"></a>Clause SELECT

Chaque requête se compose d’une clause SELECT et FROM optionnelle d’et les clauses WHERE, conformément aux normes ANSI SQL. En règle générale, la source dans la clause FROM est énumérée, et la clause WHERE applique un filtre sur la source pour récupérer un sous-ensemble d’éléments JSON. La clause SELECT projette ensuite les valeurs JSON demandées dans la liste de sélection. Pour plus d’informations sur la syntaxe, consultez [instruction SELECT](sql-api-query-reference.md#select-query).

L’instruction SELECT suivante renvoie les exemple `address` de `Families` dont `id` correspond à `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Accesseur de propriété entre guillemets
Vous pouvez accéder aux propriétés à l’aide de l’opérateur de propriété entre guillemets []. Par exemple, `SELECT c.grade` and `SELECT c["grade"]` sont équivalentes. Cette syntaxe est utile pour l’échappement d’une propriété qui contient des espaces, des caractères spéciaux, ou a le même nom qu’un mot clé SQL ou un mot réservé.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Propriétés imbriquées

L’exemple suivant projette deux propriétés imbriquées, `f.address.state` et `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>Expressions de JSON

Projection prend également en charge les expressions JSON, comme indiqué dans l’exemple suivant :

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Dans l’exemple précédent, la clause SELECT a besoin créer un objet JSON, et étant donné que l’exemple ne fournit aucune clé, la clause utilise le nom de variable d’argument implicite `$1`. La requête suivante renvoie deux variables d’argument implicites : `$1` et `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>Mot clé VALUE

Le mot clé VALUE fournit un moyen de retourner la valeur JSON autonome. Par exemple, la requête ci-dessous retourne l’expression scalaire `"Hello World"` au lieu de `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

La requête suivante retourne les valeurs JSON sans le `address` étiquette :

```sql
    SELECT VALUE f.address
    FROM Families f
```

Les résultats sont :

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

L’exemple suivant montre comment retourner des valeurs primitives JSON (le niveau feuille de l’arborescence JSON) :


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Les résultats sont :

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="DistinctKeyword"></a>Mot clé DISTINCT

Le mot clé DISTINCT élimine les doublons dans la projection de la requête.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Dans cet exemple, la requête projette les valeurs pour chaque nom de famille.

Les résultats sont :

```json
[
    "Andersen"
]
```

Vous pouvez également projeter des objets uniques. Dans ce cas, le champ nom n’existe pas dans un des deux documents, par conséquent, la requête retourne un objet vide.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Les résultats sont :

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT peut également être utilisé dans la projection dans une sous-requête :

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Cette requête projette un tableau qui contient les givenName de chaque enfant en supprimant les doublons. Ce tableau a pour alias ChildNames et projetés dans la requête externe.

Les résultats sont :

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="aliasing"></a>Alias

Vous pouvez explicitement alias valeurs dans les requêtes. Si une requête a deux propriétés portant le même nom, utilisez des alias pour renommer les moins le des propriétés afin qu’ils vous éviter toute ambiguïtés dans le résultat projeté.

Le mot clé utilisé pour l’utilisation d’alias est facultatif, comme indiqué dans l’exemple suivant, lors de la projection de la deuxième valeur en tant que `NameInfo`:

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

## <a id="FromClause"></a>Clause FROM

FROM (`FROM <from_specification>`) clause est facultative, sauf si la source est filtrée ou projetée plus loin dans la requête. Pour plus d’informations sur la syntaxe, consultez [à partir de la syntaxe](sql-api-query-reference.md#bk_from_clause). Une requête comme `SELECT * FROM Families` énumère l’ensemble `Families` conteneur. Vous pouvez également utiliser l’identificateur ROOT spécial pour le conteneur au lieu d’utiliser le nom du conteneur.

La clause FROM applique les règles suivantes par requête :

* Le conteneur peut être un alias, tel que `SELECT f.id FROM Families AS f` ou simplement `SELECT f.id FROM Families f`. Ici `f` est l’alias de `Families`. COMME est un mot clé facultatif à l’alias de l’identificateur.  

* Une fois l’alias appliqué, le nom de source d’origine ne peut pas être lié. Par exemple, `SELECT Families.id FROM Families f` syntaxiquement non valide, car l’identificateur `Families` a été ajouté en alias et ne peut plus être résolu.  

* Toutes les propriétés référencées doivent être qualifiées complets, afin d’éviter toute liaison ambiguë en l’absence de conformité de schéma strict. Par exemple, `SELECT id FROM Families f` syntaxiquement non valide, car la propriété `id` n’est pas lié.

### <a name="get-subitems-by-using-the-from-clause"></a>Obtenir des sous-éléments à l’aide de la clause FROM

La clause FROM peut réduire la source à un sous-ensemble plus petit. Pour énumérer uniquement une sous-arborescence dans chaque élément, le sous-dossier racine peut devenir la source, comme indiqué dans l’exemple suivant :

```sql
    SELECT *
    FROM Families.children
```

Les résultats sont :

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

La requête précédente a utilisé un tableau comme source, mais vous pouvez également utiliser un objet comme source. La requête prend en compte toute valeur JSON valide, défini dans la source pour être inclus dans le résultat. L’exemple suivant exclut `Families` qui n’ont pas un `address.state` valeur.

```sql
    SELECT *
    FROM Families.address.state
```

Les résultats sont :

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Clause WHERE

La clause WHERE facultative (`WHERE <filter_condition>`) spécifie les conditions que les éléments JSON source doivent satisfaire pour la requête pour les inclure dans les résultats. Un élément JSON doit évaluer les conditions spécifiées comme `true` à prendre en compte pour le résultat. La couche de l’index utilise la clause WHERE pour déterminer le plus petit sous-ensemble d’éléments source qui peut faire partie du résultat. Pour plus d’informations sur la syntaxe, consultez [syntaxe WHERE](sql-api-query-reference.md#bk_where_clause).

La requête suivante des éléments de demandes qui contiennent un `id` propriété dont la valeur est `AndersenFamily`. Il exclut tout élément qui n’a pas un `id` propriété ou dont la valeur ne correspond pas à `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Expressions scalaires dans la clause WHERE

L'exemple précédent illustrait une simple requête d'égalité. L’API SQL prend également en charge divers [expressions scalaires](#scalar-expressions). Les plus répandues sont les expressions binaires et unaires. Les références de propriété de l'objet JSON source sont également des expressions valides.

Vous pouvez utiliser les opérateurs binaires pris en charge suivants :  

|**Type d’opérateur**  | **Valeurs** |
|---------|---------|
|Opérateurs arithmétiques | +,-,*,/,% |
|Opérateurs au niveau du bit    | \|, &, ^, <<, >>, >>> (décalage vers la droite avec remplissage de zéros) |
|Opérateurs logiques    | AND, OR, NOT      |
|Comparaison | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (concaténer) |

Les requêtes suivantes utilisent les opérateurs binaires :

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Vous pouvez également utiliser les opérateurs unaires +,-, ~ et pas dans les requêtes, comme indiqué dans les exemples suivants :

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Vous pouvez également utiliser des références de propriété dans les requêtes. Par exemple, `SELECT * FROM Families f WHERE f.isRegistered` retourne l’élément JSON contenant la propriété `isRegistered` avec une valeur égale à `true`. Toute autre valeur, telle que `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, ou `<array>`, exclut l’élément à partir du résultat. 

### <a name="equality-and-comparison-operators"></a>Opérateurs d'égalité et de comparaison

Le tableau suivant répertorie les résultats des comparaisons d'égalité dans l’API SQL entre deux types JSON.

| **Opérateur** | **Undefined** | **Null** | **Booléen** | **Nombre** | **Chaîne** | **Object** | **Tableau** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Non défini | Non défini | Non défini | Non défini | Non défini | Non défini | Non défini |
| **Null** | Non défini | **OK** | Non défini | Non défini | Non défini | Non défini | Non défini |
| **Booléen** | Non défini | Non défini | **OK** | Non défini | Non défini | Non défini | Non défini |
| **Nombre** | Non défini | Non défini | Non défini | **OK** | Non défini | Non défini | Non défini |
| **Chaîne** | Non défini | Non défini | Non défini | Non défini | **OK** | Non défini | Non défini |
| **Object** | Non défini | Non défini | Non défini | Non défini | Non défini | **OK** | Non défini |
| **Tableau** | Non défini | Non défini | Non défini | Non défini | Non défini | Non défini | **OK** |

Pour les opérateurs de comparaison tels que `>`, `>=`, `!=`, `<`, et `<=`, comparaison entre les types ou entre deux objets ou tableaux produit `Undefined`.  

Si le résultat de l’expression scalaire est `Undefined`, l’élément n’est pas inclus dans le résultat, car `Undefined` n’est pas égal `true`.

### <a name="logical-and-or-and-not-operators"></a>Opérateurs logiques (AND, OR et NOT)

Les opérateurs logiques interviennent sur des valeurs booléennes. Les tableaux suivants indiquent les tables de vérité logiques de ces opérateurs :

**Opérateur OR**

| Ou | True | False | Non défini |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Non défini |
| Non défini |True |Non défini |Non défini |

**Opérateur OR**

| AND | True | False | Non défini |
| --- | --- | --- | --- |
| True |True |False |Non défini |
| False |False |False |False |
| Non défini |Non défini |False |Non défini |

**Opérateur NOT**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Non défini |Non défini |

## <a name="between-keyword"></a>Mot clé BETWEEN

Comme dans ANSI SQL, vous pouvez utiliser le mot clé BETWEEN pour exprimer des requêtes sur des plages de valeurs de chaîne ou numérique. Par exemple, la requête suivante retourne tous les éléments dans lequel note du premier enfant est 1-5, inclusif.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Contrairement à dans ANSI SQL, vous pouvez également utiliser la clause BETWEEN dans la clause FROM, comme dans l’exemple suivant.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Dans l’API SQL, contrairement à SQL ANSI, vous pouvez exprimer des requêtes de plage sur les propriétés de types mixtes. Par exemple, `grade` peut être une comme nombre `5` certains éléments et une chaîne comme `grade4` dans d’autres. Dans ce cas, comme dans JavaScript, les résultats de la comparaison entre les deux types différents dans `Undefined`, de sorte que l’élément est ignoré.

> [!TIP]
> Pour la requête d’exécution plus rapide, créez une stratégie d’indexation qui utilise un type d’index de plage par rapport à des propriétés numériques ou des chemins d’accès qui filtre la clause BETWEEN.

## <a name="in-keyword"></a>Mot clé IN

Utilisez le mot clé IN pour vérifier si une valeur spécifiée correspond à n’importe quelle valeur dans une liste. Par exemple, la requête suivante retourne tous les éléments de famille où le `id` est `WakefieldFamily` ou `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

L’exemple suivant retourne tous les éléments où l’état est une des valeurs spécifiées :

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* opérateur

L’opérateur spécial * l’ensemble des projets élément en l’état. Une fois utilisé, il doit être le seul champ projeté. Une requête comme `SELECT * FROM Families f` est valide, mais `SELECT VALUE * FROM Families f` et `SELECT *, f.id FROM Families f` ne sont pas valides. Le [commencer par interroger dans cet article](#query-the-json-items) utilisé le * opérateur. 

## <a name="-and--operators"></a>? et ?? Opérateurs

Vous pouvez utiliser le Ternary ( ?) et Coalesce ( ?) des opérateurs pour créer des expressions conditionnelles, comme dans la programmation des langages tels que C# et JavaScript. 

Vous pouvez utiliser le ? opérateur pour construire des nouvelles propriétés JSON à la volée. Par exemple, la requête suivante classifie les niveaux de qualité dans `elementary` ou `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Vous pouvez également imbriquer des appels à la ? opérateur, comme dans la requête suivante : 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Comme avec d’autres opérateurs de requête, le ? opérateur exclut des éléments si les propriétés référencées sont manquantes ou les types comparés sont différents.

Utilisez le ?? opérateur pour vérifier une propriété dans un élément lors de l’interrogation sur des données semi-structurées ou type mixte. Par exemple, la requête suivante retourne `lastName` le cas échéant, ou `surname` si `lastName` n’est pas présent.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>Opérateur TOP

Le mot clé TOP retourne le premier `N` nombre de résultats de la requête dans un ordre non défini. Comme meilleure pratique, utilisez haut avec la clause ORDER BY pour limiter les résultats à la première `N` nombre de valeurs ordonnés. Combinaison de ces deux clauses est la seule façon d’indiquer de manière prévisible les lignes affecte supérieur.

Vous pouvez utiliser TOP avec une valeur constante, comme dans l’exemple suivant, ou avec une valeur de la variable à l’aide de requêtes paramétrables. Pour plus d’informations, consultez le [des requêtes paramétrables](#parameterized-queries) section.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Les résultats sont :

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>Clause ORDER BY

Comme dans ANSI SQL, vous pouvez inclure une clause ORDER BY facultative dans les requêtes. L’argument ASC ou DESC facultatif spécifie s’il faut récupérer les résultats dans l’ordre croissant ou décroissant. ASC est la valeur par défaut.

Par exemple, voici une requête qui Récupère les familles dans l’ordre croissant de nom de la ville de résidence :

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

La requête suivante récupère la famille `id`s dans l’ordre de leur date de création d’élément. Élément `creationDate` est un nombre représentant le *heure d’époque*, ou le temps écoulé depuis le 1er janvier 1970 en secondes.

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

En outre, vous pouvez commander plusieurs propriétés. Une requête qui trie par plusieurs propriétés requiert un [index composite](index-policy.md#composite-indexes). Examinez la requête suivante :

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Cette requête récupère la famille `id` dans l’ordre croissant de nom de ville. Si plusieurs éléments ont le même nom de ville, la requête sera commander par le `creationDate` dans l’ordre décroissant.

## <a id="OffsetLimitClause"></a>Clause de limite de décalage

LIMITE de décalage est une clause facultative à ignorer, puis prendre certains nombre de valeurs à partir de la requête. Le nombre de décalage et le nombre limite sont requis dans la clause de limite de décalage.

Lors de la limite de décalage est utilisée conjointement avec une clause ORDER BY, le jeu de résultats est généré en effectuant l’ignorer et effectuez sur les valeurs ordonnées. Si aucune clause ORDER BY n’est utilisée, cela entraînerait un ordre déterministe des valeurs.

Par exemple, voici une requête qui ignore la première valeur et retourne la deuxième valeur (dans l’ordre du nom de la ville de résidence) :

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Les résultats sont :

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Voici une requête qui ignore la première valeur et retourne la deuxième valeur (sans classement) :

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Les résultats sont :

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```




## <a name="scalar-expressions"></a>Expressions scalaires

La clause SELECT prend en charge les expressions scalaires telles que des constantes, des expressions arithmétiques et des expressions logiques. La requête suivante utilise une expression scalaire :


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Les résultats sont :

```json
    [{
      "$1": 1.33333
    }]
```

Dans la requête suivante, le résultat de l’expression scalaire est une valeur booléenne :


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Les résultats sont :

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>Création d'objet et de tableau

Une fonctionnalité clé de l’API SQL est la création de tableau et d’objet. L’exemple précédent a créé un nouvel objet JSON, `AreFromSameCityState`. Vous pouvez également construire des tableaux, comme indiqué dans l’exemple suivant :


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Les résultats sont :

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

La requête SQL suivante est un autre exemple de l’utilisation de tableau dans les sous-requêtes. Cette requête obtient tous les noms donnés distinctes d’enfants dans un tableau.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```


## <a id="Iteration"></a>Itération

L’API SQL prend en charge pour itérer sur des tableaux JSON, avec une nouvelle construction ajoutée via le mot clé IN dans la source de FROM. Dans l’exemple suivant :

```sql
    SELECT *
    FROM Families.children
```

Les résultats sont :

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

La requête suivante effectue une itération sur `children` dans le `Families` conteneur. Le tableau de sortie est différent de la requête précédente. Cet exemple fractionne `children`et regroupe les résultats en un seul tableau :  

```sql
    SELECT *
    FROM c IN Families.children
```

Les résultats sont :

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Vous pouvez filtrer les informations supplémentaires sur chaque entrée du tableau, comme indiqué dans l’exemple suivant :

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Les résultats sont :

```json
    [{
      "givenName": "Lisa"
    }]
```

Vous pouvez également agréger sur le résultat d’une itération de tableau. Par exemple, la requête suivante compte le nombre d’enfants parmi toutes les familles de :

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Les résultats sont :

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Jointures

Dans une base de données relationnelle, les jointures entre les tables sont la conséquence logique de la conception de schémas normalisés. En revanche, l’API SQL utilise le modèle de données dénormalisées d’éléments de schéma, qui est l’opérateur logique équivalent d’un *jointure réflexive*.

Le langage prend en charge la syntaxe `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Cette requête retourne un jeu de tuples avec `N` valeurs. Les valeurs de chaque tuple sont produites par l’itération de tous les alias du conteneur sur leurs ensembles respectifs. En d’autres termes, cette requête effectue un produit croisé complet des ensembles participant à la jointure.

Les exemples suivants illustrent le fonctionnement de la clause JOIN. Dans l’exemple suivant, le résultat est vide, depuis le produit croisé de chaque élément à partir de la source et un jeu vide est vide :

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Le résultat est le suivant :

```json
    [{
    }]
```

Dans l’exemple suivant, la jointure est un produit croisé entre deux objets JSON, la racine de l’élément `id` et `children` sous-racine. Le fait que `children` est un tableau n’est pas efficace dans la jointure, car il porte sur une seule racine qui est le `children` tableau. Le résultat contient uniquement deux résultats, car le produit croisé de chaque élément avec le tableau donne exactement un seul élément.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Les résultats sont :

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

L'exemple suivant illustre une jointure plus conventionnelle :

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Les résultats sont :

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

La source FROM de la clause de jointure est un itérateur. Par conséquent, le flux dans l’exemple précédent est :  

1. Développez chaque élément enfant `c` dans le tableau.
2. Appliquez le produit croisé avec la racine de l’élément `f` avec chaque élément enfant `c` aplatie de la première étape.
3. Enfin, l’objet racine de projet `f` `id` seule la propriété.

Le premier élément, `AndersenFamily`, contient un seul `children` élément, par conséquent, le jeu de résultats contient un seul objet. Le deuxième élément `WakefieldFamily`, contient deux `children`, de sorte que le produit croisé produit deux objets, un pour chaque `children` élément. Les champs racine de ces deux éléments sont identiques, comme on peut l’attendre d’un produit croisé.

La véritable utilité de la clause de jointure consiste à former des tuples dans le produit croisé dans une forme qui serait autrement difficile au projet. L’exemple ci-dessous, les filtres sur la combinaison d’un tuple qui permet à l’utilisateur de choisir une condition satisfaite par les tuples globales.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Les résultats sont :

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

L’extension suivante de l’exemple précédent effectue une double jointure. Vous pouvez afficher le produit croisé comme le pseudo-code suivant :

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` possède un enfant qui a un animal domestique, donc le produit croisé renvoie une ligne (1\*1\*1) à partir de cette famille. `WakefieldFamily` a deux enfants, seul qui a des animaux, mais cet enfant a deux animaux. Le produit croisé pour cette famille de produit 1\*1\*2 = 2 lignes.

Dans l’exemple suivant, il existe un filtre supplémentaire sur `pet`, ce qui exclut tous les tuples où le nom de l’animal de compagnie n’est pas `Shadow`. Vous pouvez développer des tuples à partir de tableaux, filtrer sur un des éléments du tuple et n’importe quelle combinaison des éléments de projet.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Les résultats sont :

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>Fonctions définies par l’utilisateur (UDF)

L’API SQL prend en charge les fonctions définies par l’utilisateur (UDF). Avec les fonctions UDF scalaires, vous pouvez passer des arguments de zéro ou plusieurs et retourner un résultat unique argument. L’API vérifie chaque argument de légalité des valeurs JSON.  

L’API étend la syntaxe SQL pour prendre en charge la logique d’application personnalisée à l’aide des fonctions UDF. Vous pouvez enregistrer des UDF avec l’API SQL et les référencer dans les requêtes SQL. En fait, les fonctions définies par l’utilisateur sont conçues avec soin pour être appelées à partir de requêtes. En corollaire, UDF n’ont pas d’accès à l’objet de contexte comme d’autres types JavaScript, telles que des procédures stockées et déclencheurs. Les requêtes sont en lecture seule et peuvent s’exécuter sur des réplicas primaires ou secondaires. UDF, contrairement à d’autres types JavaScript, sont conçus pour s’exécuter sur les réplicas secondaires.

L’exemple suivant inscrit une fonction UDF sous un conteneur d’éléments dans la base de données Cosmos DB. L’exemple crée une fonction UDF dont le nom est `REGEX_MATCH`. Il accepte deux valeurs de chaîne JSON, `input` et `pattern`, et vérifie si la première correspond au modèle spécifié dans la seconde à l’aide de JavaScript `string.match()` (fonction).

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Maintenant, utilisez cette fonction UDF dans une projection de requête. Vous devez qualifier l’UDF avec le préfixe respectant la casse `udf.` lors de l’appel à partir de requêtes.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Les résultats sont :

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Vous pouvez utiliser la fonction UDF qualifiée avec le `udf.` préfixe à l’intérieur d’un filtre, comme dans l’exemple suivant :

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Les résultats sont :

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

En bref, définies par le sont des expressions scalaires valides que vous pouvez utiliser dans les filtres et des projections.

Pour développer la puissance des UDF, examinez un autre exemple avec une logique conditionnelle :

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

L’exemple suivant teste la fonction UDF :

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Les résultats sont :

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Si les propriétés de référence par la fonction UDF paramètres ne sont pas disponibles dans la valeur JSON, le paramètre est considéré comme non défini et l’appel de fonction UDF est ignorée. De même, si le résultat de la fonction UDF n’est pas défini, il n’est pas inclus dans le résultat.

Comme le montrent les exemples précédents, définies par l’intègrent la puissance du langage JavaScript dans l’API SQL. UDF fournissent une interface programmable enrichie pour faire une logique procédurale, conditionnelle complexe à l’aide de fonctionnalités de runtime JavaScript intégrées. L’API SQL fournit les arguments aux UDF pour chaque élément de la source au WHERE actuel ou à la clause SELECT étape du traitement. Le résultat est intégré en toute transparence dans le pipeline d’exécution globale. En résumé, UDF sont des outils efficaces pour effectuer une logique métier complexe dans le cadre de requêtes.

## <a id="Aggregates"></a>Fonctions d’agrégation

Fonctions d’agrégation effectuent un calcul sur un ensemble de valeurs dans la clause SELECT et retournent une valeur unique. Par exemple, la requête suivante retourne le nombre d’éléments dans le `Families` conteneur :

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

Vous pouvez également retourner uniquement la valeur scalaire de l’agrégat en utilisant le mot clé VALUE. Par exemple, la requête suivante renvoie le nombre de valeurs sous forme de nombre unique :

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Les résultats sont :

```json
    [ 2 ]
```

Vous pouvez également combiner des agrégations avec des filtres. Par exemple, la requête suivante retourne le nombre d’éléments avec l’état de l’adresse de `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Les résultats sont :

```json
    [ 1 ]
```

L’API SQL prend en charge les fonctions d’agrégation suivantes. SUM et AVG opèrent sur des valeurs numériques, et COUNT, MIN et MAX travailler sur des nombres, des chaînes, des valeurs booléennes et les valeurs NULL.

| Fonction | Description  |
|-------|-------------|
| NOMBRE | Renvoie le nombre d’éléments que contient l’expression. |
| SUM   | Renvoie la somme de toutes les valeurs de l’expression. |
| MIN   | Renvoie la valeur minimale de l’expression. |
| MAX   | Renvoie la valeur maximale de l’expression. |
| AVG   | Renvoie la moyenne des valeurs de l’expression. |

Vous pouvez également agréger les résultats d’une itération de tableau. Pour plus d’informations, consultez le [itération](#Iteration) section.

> [!NOTE]
> Dans l’Explorateur de données du portail Azure, les requêtes d’agrégation peuvent agréger des résultats partiels de la requête qu’une seule page. Le Kit de développement produit une valeur cumulée unique sur toutes les pages. Pour effectuer des requêtes d’agrégation à l’aide de code, vous devez .NET SDK 1.12.0, le SDK .NET Core 1.1.0 ou du SDK Java 1.9.5 ou version ultérieure.
>

## <a id="BuiltinFunctions"></a>Fonctions intégrées

COSMOS DB prend également en charge un nombre de fonctions intégrées pour les opérations courantes, vous pouvez utiliser dans les requêtes comme les fonctions définies par l’utilisateur (UDF).

| Groupe de fonctions | Opérations |
|---------|----------|
| Fonctions mathématiques | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Fonctions de vérification de type | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Fonctions de chaîne | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Fonctions de tableau | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH et ARRAY_SLICE |
| Fonctions spatiales | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Si vous utilisez actuellement une fonction définie par l’utilisateur (UDF) pour laquelle une fonction intégrée est désormais disponible, la fonction intégrée correspondante sera exécutera plus rapidement et plus efficace.

La principale différence entre les fonctions de Cosmos DB et SQL ANSI est que les fonctions de Cosmos DB sont conçues pour fonctionner avec des données sans schéma et de schéma mixte. Par exemple, si une propriété est manquante ou a une valeur non numérique comme `unknown`, l’élément est ignorée au lieu de renvoyer une erreur.

### <a name="mathematical-functions"></a>Fonctions mathématiques

Chaque fonction mathématique effectue un calcul, basé sur les valeurs d’entrée fournies comme arguments, et renvoie une valeur numérique. Ce tableau répertorie les fonctions mathématiques intégrées qui sont prises en charge.

| Usage | Description  |
|----------|--------|
| ABS (num_expr) | Retourne la valeur (positive) absolue de l'expression numérique spécifiée. |
| CEILING (num_expr) | Retourne le plus petit nombre entier qui est supérieur ou égal à l'expression numérique spécifiée. |
| FLOOR (num_expr) | Retourne le plus grand nombre entier qui est inférieur ou égal à l'expression numérique spécifiée. |
| EXP (num_expr) | Retourne l'exposant de l'expression numérique spécifiée. |
| JOURNAL (num_expr, base) | Retourne le logarithme népérien de l’expression numérique spécifiée, ou le logarithme à l’aide de la base spécifiée. |
| LOG10 (num_expr) | Retourne le logarithme en base 10 de l'expression numérique spécifiée. |
| ROUND (num_expr) | Retourne une valeur numérique, arrondie au nombre entier le plus proche. |
| TRUNC (num_expr) | Retourne une valeur numérique, tronquée au nombre entier le plus proche. |
| SQRT (num_expr) | Retourne la racine carrée de l'expression numérique spécifiée. |
| SQUARE (num_expr) | Retourne le carré de l'expression numérique spécifiée. |
| POWER (num_expr, num_expr) | Renvoie un nombre spécifié élevé à la puissance spécifiée. |
| SIGN (num_expr) | Retourne la valeur du signe (-1, 0, 1) de l'expression numérique spécifiée. |
| ACOS (num_expr) | Retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelée arccosinus. |
| ASIN (num_expr) | Retourne l’angle, en radians, dont le sinus est l’expression numérique spécifiée. Cette fonction est également appelée arcsinus. |
| ATAN (num_expr) | Retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée. Cette fonction est également appelée arctangente. |
| ATN2 (num_expr) | Retourne l’angle, en radians, entre l’axe des abscisses positives et le rayon depuis l’origine vers le point (y, x), où x et y sont les valeurs des deux expressions flottantes spécifiées. |
| COS (num_expr) | Retourne le cosinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée. |
| COT (num_expr) | Retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée. |
| DEGREES (num_expr) | Retourne l’angle correspondant en degrés d’un angle spécifié en radians. |
| PI () | Retourne la valeur constante de PI. |
| RADIANS (num_expr) | Retourne des radians lorsqu’une expression numérique, en degrés, est entrée. |
| SIN (num_expr) | Retourne le sinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée. |
| TAN (num_expr) | Retourne la tangente de l’expression d’entrée, dans l’expression spécifiée. |

Vous pouvez exécuter des requêtes comme dans l’exemple suivant :

```sql
    SELECT VALUE ABS(-4)
```

Le résultat est le suivant :

```json
    [4]
```

### <a name="type-checking-functions"></a>Fonctions de vérification de type

Les fonctions de vérification de type vous permettent de vérifier le type d’une expression au sein d’une requête SQL. Vous pouvez utiliser les fonctions de vérification de type pour déterminer les types de propriétés au sein des éléments à la volée, lorsqu’ils sont variable ou inconnu. Voici un tableau des fonctions de la vérification des types intégrés pris en charge :

| **Utilisation** | **Description** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | Retourne une valeur booléenne indiquant si la valeur est du type tableau. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | Retourne une valeur booléenne indiquant si la valeur est du type booléen. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Retourne une valeur booléenne indiquant si la valeur est du type null. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | Retourne une valeur booléenne indiquant si la valeur est du type numérique. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | Retourne une valeur booléenne indiquant si la valeur est du type objet JSON. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | Retourne une valeur booléenne indiquant si la valeur est du type chaîne. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | Retourne une valeur booléenne indiquant si une valeur a été attribuée à la propriété. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Retourne une valeur booléenne indiquant si le type de la valeur est une chaîne, nombre, booléen ou null. |

À l’aide de ces fonctions, vous pouvez exécuter des requêtes comme dans l’exemple suivant :

```sql
    SELECT VALUE IS_NUMBER(-4)
```

Le résultat est le suivant :

```json
    [true]
```

### <a name="string-functions"></a>Fonctions de chaîne

Les fonctions scalaires suivantes effectuent une opération sur une valeur de chaîne d’entrée et retournent une valeur de chaîne, numérique ou booléenne. Voici un tableau des fonctions de chaîne intégrées :

| Usage | Description  |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Retourne le nombre de caractères de l’expression de chaîne spécifiée. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Retourne une chaîne qui est le résultat de la concaténation d’au moins deux valeurs de chaîne. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Retourne une partie d’une expression de chaîne. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Retourne une valeur booléenne indiquant si la première expression de chaîne commence par la seconde. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Retourne la position de départ de la première occurrence de la seconde expression de chaîne dans la première expression de chaîne spécifiée, ou -1 si la chaîne n’est pas trouvée. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Retourne la partie gauche d’une chaîne avec le nombre de caractères spécifié. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Retourne la partie droite d’une chaîne avec le nombre de caractères spécifié. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Retourne une expression de chaîne après avoir supprimé les espaces de début. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Retourne une expression de chaîne après avoir tronqué tous les espaces de fin. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Retourne une expression de chaîne après la conversion des caractères majuscules en caractères minuscules. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Retourne une expression de chaîne après la conversion des caractères minuscules en caractères majuscules. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Remplace toutes les occurrences d’une valeur de chaîne spécifiée par une autre valeur de chaîne. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Répète une valeur de chaîne un nombre de fois spécifié. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Retourne l’ordre inverse d’une valeur de chaîne. |

À l’aide de ces fonctions, vous pouvez exécuter des requêtes comme les suivantes, qui retourne la famille `id` en majuscules :

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Les résultats sont :

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Ou concaténer des chaînes, comme dans cet exemple :

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Les résultats sont :

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Vous pouvez également utiliser les fonctions de chaîne dans la clause WHERE pour filtrer les résultats, comme dans l’exemple suivant :

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Les résultats sont :

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Fonctions de tableau

Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de tableau et retournent une valeur numérique, booléen ou valeur de tableau. Voici un tableau des fonctions de tableau intégrées :

| Usage | Description  |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Retourne le nombre d’éléments de l’expression de tableau spécifiée. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Retourne un tableau qui est le résultat de la concaténation d’au moins deux valeurs de tableau. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Retourne une valeur booléenne qui indique si le tableau contient la valeur spécifiée. Peut spécifier si la correspondance est totale ou partielle. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Retourne une partie d’une expression de tableau. |

Utilisez les fonctions de tableau pour manipuler des tableaux dans JSON. Par exemple, voici une requête qui retourne tous les éléments `id`s où une de le `parents` est `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

Le résultat est le suivant :

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Vous pouvez spécifier un fragment partiel pour faire correspondre des éléments dans le tableau. La requête suivante recherche tous les éléments `id`s ayant `parents` avec la `givenName` de `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

Le résultat est le suivant :

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Voici un autre exemple qui utilise ARRAY_LENGTH pour obtenir le nombre de `children` par famille :

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Les résultats sont :

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Fonctions spatiales

COSMOS DB prend en charge les fonctions intégrées Open Geospatial Consortium (OGC) suivantes pour les requêtes géospatiales : 

| Usage | Description  |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Retourne la distance entre les deux GeoJSON `Point`, `Polygon`, ou `LineString` expressions. |
| T_WITHIN (point_expr, polygon_expr) | Retourne une expression booléenne indiquant si le premier objet GeoJSON (`Point`, `Polygon`, ou `LineString`) se trouve dans le second objet GeoJSON (`Point`, `Polygon`, ou `LineString`). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Retourne une expression booléenne indiquant si les deux spécifiés d’objets GeoJSON (`Point`, `Polygon`, ou `LineString`) se croisent. |
| ST_ISVALID | Retourne une valeur booléenne indiquant si le GeoJSON spécifié `Point`, `Polygon`, ou `LineString` expression est valide. |
| ST_ISVALIDDETAILED | Retourne une valeur JSON contenant une valeur booléenne si le GeoJSON spécifié `Point`, `Polygon`, ou `LineString` expression est valide et si elle est non valide, la raison en tant que valeur de chaîne. |

Vous pouvez utiliser les fonctions spatiales pour effectuer des requêtes de proximité sur les données spatiales. Par exemple, voici une requête qui retourne tous les éléments de famille qui sont dans un rayon de 30 kilomètres d’un emplacement spécifié à l’aide de la fonction intégrée ST_DISTANCE :

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

Le résultat est le suivant :

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Pour plus d’informations sur la prise en charge géospatiale dans Cosmos DB, consultez [Utiliser des données géospatiales dans Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>Requêtes paramétrables

COSMOS DB prend en charge les requêtes avec des paramètres exprimées par la notation @ classique. SQL paramétré fournit une gestion robuste et la séquence d’échappement des entrées d’utilisateur et empêche l’exposition accidentelle des données via l’injection SQL.

Par exemple, vous pouvez écrire une requête qui accepte `lastName` et `address.state` en tant que paramètres et exécutez-le pour différentes valeurs de `lastName` et `address.state` basée sur l’entrée d’utilisateur.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Vous pouvez ensuite envoyer cette demande à Cosmos DB comme requête JSON paramétrable comme suit :

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

L’exemple suivant définit l’argument supérieur avec une requête paramétrée : 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Les valeurs de paramètre peuvent être n’importe quel format JSON valide : chaînes, nombres, valeurs booléennes, null, même des tableaux ou du code JSON imbriqué. Étant donné que Cosmos DB est sans schéma, les paramètres ne sont pas validés par rapport à n’importe quel type.

## <a id="JavaScriptIntegration"></a>Intégration JavaScript

Azure Cosmos DB fournit un modèle de programmation pour l’exécution de la logique d’application basée sur JavaScript directement sur les conteneurs, à l’aide de procédures stockées et les déclencheurs. Ce modèle prend en charge :

* Les opérations CRUD transactionnelles hautes performances et les requêtes sur les éléments dans un conteneur, en vertu de l’intégration approfondie de l’exécution de JavaScript dans le moteur de base de données.
* Une modélisation naturelle du flux de contrôle, variable de portée et l’attribution et l’intégration des primitives de gestion des exceptions avec des transactions de base de données. 

Pour plus d’informations sur l’intégration d’Azure Cosmos DB JavaScript, consultez le [interface API JavaScript côté serveur](#JavaScriptServerSideApi) section.

### <a name="operator-evaluation"></a>Évaluation d'opérateur

COSMOS DB, en vertu de la base de données JSON, établir des correspondances avec les opérateurs JavaScript et sémantique d’évaluation. COSMOS DB tente de préserver la sémantique JavaScript en termes de prise en charge JSON, mais l’opération d’évaluation dévie dans certains cas.

Dans l’API SQL, contrairement au langage SQL classique, les types de valeurs sont souvent inconnus jusqu'à ce que l’API extrait les valeurs de la base de données. Afin d'exécuter les requêtes de manière efficace, la plupart des opérateurs ont des exigences de type strictes.

Contrairement à JavaScript, l’API SQL n’effectue pas de conversions implicites. Par exemple, une requête comme `SELECT * FROM Person p WHERE p.Age = 21` correspond à des éléments qui contiennent un `Age` propriété dont la valeur est `21`. Il ne correspond à aucun autre élément dont la propriété `Age` propriété correspond à des variations éventuellement infinies comme `twenty-one`, `021`, ou `21.0`. Ceci contraste avec JavaScript, où les valeurs de chaîne sont implicitement convertis en nombres basée sur un opérateur, par exemple : `==`. Ce comportement de l’API SQL est essentiel pour la mise en correspondance d’index efficace.

## <a id="ExecutingSqlQueries"></a>Exécution des requêtes SQL

N’importe quel langage capable de créer des requêtes HTTP/HTTPS peut appeler l’API REST de Cosmos DB. COSMOS DB offre également des bibliothèques de programmation pour les langages de programmation .NET, Node.js, JavaScript et Python. L’API REST et toutes les bibliothèques prennent en charge interrogation via SQL, et le kit SDK .NET prend également en charge [l’interrogation LINQ](#Linq).

Les exemples suivants montrent comment créer une requête et la soumettre à un compte de base de données Cosmos DB.

### <a id="RestAPI"></a>API REST

Cosmos DB fournit un modèle de programmation RESTful ouvert sur HTTP. Le modèle de ressource se compose d’un ensemble de ressources sous un compte de base de données, ce qui met en service un abonnement Azure. Le compte de base de données se compose d’un ensemble de *bases de données*, chacun d’eux peut contenir plusieurs *conteneurs*, qui à son tour contenir *éléments*, UDF et autres types de ressources. Chaque ressource de Cosmos DB est adressable à l’aide d’un URI stable et logique. Un ensemble de ressources est appelé un *flux*. 

Le modèle d’interaction de base avec ces ressources est via les verbes HTTP `GET`, `PUT`, `POST`, et `DELETE`, avec leur interprétation standard. Utilisez `POST` pour créer une nouvelle ressource, exécuter une procédure stockée, ou émettre une requête Cosmos DB. Les requêtes sont toujours des opérations en lecture seule sans effets secondaires.

Les exemples suivants montrent un `POST` pour une requête API SQL par rapport à des exemples d’éléments. La requête contient un filtre simple sur le fichier JSON `name` propriété. Le `x-ms-documentdb-isquery` et Content-Type : `application/query+json` en-têtes indiquent que l’opération est une requête. Remplacez `mysqlapicosmosdb.documents.azure.com:443` par l’URI de votre compte Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Les résultats sont :

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

La requête suivante, plus complexe retourne plusieurs résultats à partir d’une jointure :

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

Les résultats sont : 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Si les résultats d’une requête ne tiennent pas dans une seule page, l’API REST retourne un jeton de liaison via le `x-ms-continuation-token` en-tête de réponse. Les clients peuvent paginer les résultats en incluant l’en-tête dans les résultats suivants. Vous pouvez également contrôler le nombre de résultats par page via la `x-ms-max-item-count` en-tête du numéro. 

Si une requête a une fonction d’agrégation telles que COUNT, la page de requête peut renvoyer une valeur partiellement agrégée sur qu’une seule page de résultats. Les clients doivent effectuer une agrégation de deuxième niveau sur ces résultats pour produire les résultats finaux. Par exemple, additionner des nombres renvoyés dans les pages individuelles pour renvoyer le nombre total.

Pour gérer la stratégie de cohérence des données pour les requêtes, utilisez le `x-ms-consistency-level` en-tête comme dans toutes les demandes d’API REST. Cohérence de session nécessite également reproduit en écho la dernière version `x-ms-session-token` en-tête de cookie dans la demande de requête. La stratégie d’indexation du conteneur interrogé peut également influencer la cohérence des résultats de la requête. Avec l’indexation des paramètres de stratégie pour les conteneurs par défaut, l’index est toujours actualisée par le contenu de l’élément et résultats de la requête correspondent à la cohérence choisie pour les données. Pour plus d’informations, consultez [niveaux de cohérence Azure Cosmos DB][consistency-levels].

Si la stratégie d’indexation configurée sur le conteneur ne peut pas prendre en charge la requête spécifiée, le serveur Azure Cosmos DB renvoie 400 « demande incorrecte ». Il retourne ce message d’erreur pour les requêtes avec chemins d’accès explicitement exclus de l’indexation. Vous pouvez spécifier le `x-ms-documentdb-query-enable-scan` en-tête pour permettre à la requête effectuer une analyse lorsqu’un index n’est pas disponible.

Vous pouvez obtenir des métriques détaillées sur l’exécution des requêtes en définissant le `x-ms-documentdb-populatequerymetrics` en-tête à `true`. Pour en savoir plus, consultez la section relative aux [métriques de requête SQL pour Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C#(SDK) .NET

Le kit SDK .NET prend en charge l'interrogation LINQ et SQL. L’exemple suivant montre comment effectuer la requête de filtre précédente avec .NET :

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

L’exemple suivant compare deux propriétés pour l’égalité au sein de chaque élément et utilise des projections anonymes.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

L’exemple suivant montre des jointures, exprimées via LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Le client .NET itère automatiquement toutes les pages des résultats de requête dans le `foreach` bloque, comme indiqué dans l’exemple précédent. Les options de requête introduite dans le [API REST](#RestAPI) section sont également disponibles dans le SDK .NET, à l’aide de la `FeedOptions` et `FeedResponse` classes dans le `CreateDocumentQuery` (méthode). Vous pouvez contrôler le nombre de pages à l’aide de le `MaxItemCount` paramètre.

Vous pouvez également contrôler explicitement la pagination en créant `IDocumentQueryable` à l’aide de la `IQueryable` objet, puis en lisant le `ResponseContinuationToken` valeurs et en les passant sauvegarder en tant que `RequestContinuationToken` dans `FeedOptions`. Vous pouvez définir `EnableScanInQuery` pour autoriser les analyses lorsque la requête n’est pas prise en charge par la stratégie d’indexation configurée. Pour les conteneurs partitionnés, vous pouvez utiliser `PartitionKey` pour exécuter la requête sur une partition unique, même si Azure Cosmos DB peut extraire automatiquement cet élément à partir du texte de requête. Vous pouvez utiliser `EnableCrossPartitionQuery` pour exécuter des requêtes sur plusieurs partitions.

Pour plus d’exemples .NET avec des requêtes, consultez la [exemples Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) dans GitHub.

### <a id="JavaScriptServerSideApi"></a>API JavaScript côté serveur

COSMOS DB fournit un modèle de programmation pour l’exécution de la logique d’application JavaScript directement sur les conteneurs, à l’aide de procédures stockées et les déclencheurs. La logique JavaScript enregistrée au niveau du conteneur peut alors émettre des opérations de base de données sur les éléments du conteneur donné, encapsulées dans des transactions ACID ambiantes.

L’exemple suivant montre comment utiliser `queryDocuments` dans l’API pour créer des requêtes à partir du serveur de JavaScript à l’intérieur des procédures stockées et déclencheurs :

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>API LINQ to SQL

LINQ est un modèle de programmation .NET qui exprime un calcul en tant que requêtes sur des flux d’objet. Cosmos DB fournit une bibliothèque côté client pour interagir avec LINQ en facilitant la conversion entre les objets JSON et .NET, ainsi qu’un mappage à partir d’un sous-ensemble de requêtes LINQ vers des requêtes Cosmos DB.

Le diagramme suivant illustre l’architecture de prise en charge des requêtes LINQ à l’aide de Cosmos DB. Utilisez le client Cosmos DB, vous pouvez créer un `IQueryable` objet directement interroge le fournisseur de requête Cosmos DB, la requête LINQ se traduit une requête Cosmos DB. Ensuite, vous transmettez la requête au serveur de Cosmos DB, qui Récupère un jeu de résultats au format JSON. Le désérialiseur JSON convertit les résultats dans un flux d’objets .NET côté client.

![Architecture de prise en charge des requêtes LINQ avec l’API SQL (syntaxe SQL, langage de requête JSON, concepts de bases de données et requêtes SQL)][1]

### <a name="net-and-json-mapping"></a>Mappage .NET et JSON

Le mappage entre les objets .NET et les éléments JSON est naturel. Chaque champ de membre de données est mappé à un objet JSON, où le nom du champ est mappé à la *clé* partie de l’objet et la valeur de manière récursive est mappé à la *valeur* dans le cadre de l’objet. Le code suivant mappe la `Family` classe à un élément JSON et crée ensuite un `Family` objet :

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

L’exemple précédent crée l’élément JSON suivant :

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>Conversion LINQ en SQL

Le fournisseur de requêtes de Cosmos DB effectue le meilleur mappage possible entre une requête LINQ et une requête SQL Cosmos DB. La description suivante suppose une connaissance de base de LINQ.

Le système de type de fournisseur de requête prend en charge uniquement les types JSON primitifs : numérique, booléen, chaîne et null. 

Le fournisseur de requête prend en charge les expressions scalaires suivantes :

- Valeurs constantes, y compris des valeurs constantes des types de données primitifs au moment de l’évaluation de requête.
  
- Propriété/tableau indexer des expressions qui font référence à la propriété d’un objet ou un élément de tableau. Exemple :
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressions arithmétiques, y compris les expressions arithmétiques communes sur les valeurs numériques et booléennes. Pour obtenir la liste complète, consultez le [spécification de SQL Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressions de comparaison de chaîne, qui incluent les comparaisons de valeur de chaîne à une valeur de chaîne constante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressions de création de tableau d’objets, qui retournent un objet de type valeur composée ou de type anonyme ou un tableau de ces objets. Vous pouvez imbriquer ces valeurs.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>Opérateurs LINQ pris en charge

Le fournisseur LINQ inclus avec le SDK .NET SQL prend en charge les opérateurs suivants :

- **Select** : Projections sont traduites en SQL SELECT, y compris la construction d’objet.
- **Where** : Filtres traduit en SQL WHERE et prennent en charge la traduction entre `&&`, `||`, et `!` aux opérateurs SQL
- **SelectMany** : autorise le déroulement de tableaux vers la clause SQL JOIN. Permet de chaîner ou imbriquer des expressions pour filtrer les éléments de tableau.
- **OrderBy** et **OrderByDescending**: Se traduire par une clause ORDER BY avec ASC ou DESC.
- Les opérateurs **Count**, **Sum**, **Min**, **Max** et **Average** pour l’agrégation, et leurs équivalents asynchrones **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** et **AverageAsync**.
- **CompareTo** : se traduit par des comparaisons de plages. Couramment utilisé pour les chaînes, car ils ne sont pas comparables dans .NET.
- **Take** : Se traduit par SQL TOP pour limiter les résultats d’une requête.
- **Fonctions mathématiques**: Prend en charge la traduction à partir de .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, et `Truncate` pour les fonctions SQL intégrées équivalentes.
- **Fonctions de chaîne**: Prend en charge la traduction à partir de .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, et `TrimStart` pour les fonctions SQL intégrées équivalentes.
- **Fonctions de tableau**: Prend en charge la traduction à partir de .NET `Concat`, `Contains`, et `Count` pour les fonctions SQL intégrées équivalentes.
- **Fonctions d’Extension de Geospatial**: Prend en charge la traduction des méthodes stub `Distance`, `IsValid`, `IsValidDetailed`, et `Within` pour les fonctions SQL intégrées équivalentes.
- **Fonction d’Extension de la fonction définie par l’utilisateur**: Prend en charge la traduction à partir de la méthode stub `UserDefinedFunctionProvider.Invoke` à la fonction définie par l’utilisateur correspondante.
- **Miscellaneous** : Prend en charge la traduction de `Coalesce` et opérateurs conditionnels. Peut traduire `Contains` en chaîne CONTAINS, ARRAY_CONTAINS ou SQL IN, selon le contexte.

### <a name="sql-query-operators"></a>Opérateurs de requête SQL

Les exemples suivants illustrent la façon dont certains des opérateurs de requête LINQ standards traduisent en requêtes Cosmos DB.

#### <a name="select-operator"></a>Sélectionner un opérateur

La syntaxe est `input.Select(x => f(x))`, où `f` est une expression scalaire.

**Sélectionnez un opérateur, exemple 1 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Sélectionnez un opérateur, exemple 2 :** 

- **Expression Lambda LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Opérateur de sélection exemple 3 :**

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

#### <a name="selectmany-operator"></a>Opérateur SelectMany

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

#### <a name="where-operator"></a>Opérateur Where

La syntaxe est `input.Where(x => f(x))`, où `f` est une expression scalaire qui retourne une valeur booléenne.

**Où opérateur, exemple 1 :**

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
  
**Où opérateur, exemple 2 :**

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

### <a name="composite-sql-queries"></a>Requêtes SQL composites

Vous pouvez composer les opérateurs précédents pour former des requêtes plus puissantes. Étant donné que Cosmos DB prend en charge les conteneurs imbriqués, vous pouvez concaténer ou imbriquer la composition.

#### <a name="concatenation"></a>Concaténation

La syntaxe est `input(.|.SelectMany())(.Select()|.Where())*`. Une requête concaténée peut commencer par un texte facultatif `SelectMany` requête, suivie de plusieurs `Select` ou `Where` opérateurs.

**Concaténation, exemple 1 :**

- **Expression Lambda LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Concaténation, exemple 2 :**

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

**Concaténation, exemple 3 :**

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

**Concaténation, exemple 4 :**

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

#### <a name="nesting"></a>Imbrication

La syntaxe est `input.SelectMany(x=>x.Q())` où `Q` est un `Select`, `SelectMany`, ou `Where` opérateur.

Une requête imbriquée applique la requête interne à chaque élément du conteneur externe. Une fonctionnalité importante est que la requête interne peut faire référence aux champs des éléments dans le conteneur externe, comme une jointure réflexive.

**Imbrication, exemple 1 :**

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

**Imbrication, exemple 2 :**

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

**Imbrication, exemple 3 :**

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

## <a id="References"></a>Références

- [Spécification SQL Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Spécification de JavaScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Techniques d’évaluation pour les grandes bases de données de requête](https://dl.acm.org/citation.cfm?id=152611). *ACM Computing Surveys* 25, aucun. 2 (1993).
- Graefe, G. « Le framework Cascades pour l’optimisation des requêtes ». *Données de l’IEEE ENG À la hausse.* 18, aucun. 3 (1995).
- Lu, Ooi, Tan. « Traitement des requêtes dans les systèmes de base de données relationnelle parallèle. » *IEEE Computer Society Press* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar et Andrew Tomkins. « Pig Latin : Un non-So-Foreign Language for Data Processing. » *SIGMOD* (2008).

## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’Azure Cosmos DB][introduction]
- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Niveaux de cohérence Azure Cosmos DB][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
