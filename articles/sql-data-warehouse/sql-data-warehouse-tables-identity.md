---
title: Using IDENTITY to create surrogate keys - Azure SQL Data Warehouse| Microsoft Docs
description: Recommendations and examples for using the IDENTITY property to create surrogate keys on tables in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ab028705f5af7c37017d2e697240b7d3436f5f71
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Using IDENTITY to create surrogate keys in Azure SQL Data Warehouse
Recommendations and examples for using the IDENTITY property to create surrogate keys on tables in Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>What is a surrogate key?
A surrogate key on a table is a column with a unique identifier for each row. The key is not generated from the table data. Data modelers like to create surrogate keys on their tables when they design data warehouse models. Vous pouvez utiliser la propriété IDENTITY pour atteindre cet objectif de manière simple et efficace, sans affecter les performances de chargement.  

## <a name="creating-a-table-with-an-identity-column"></a>Creating a table with an IDENTITY column
La propriété IDENTITY est conçue pour augmenter la taille des instances sur toutes les distributions de l’entrepôt de données sans affecter les performances de chargement. Par conséquent, l’implémentation d’IDENTITY est adaptée pour atteindre ces objectifs. 

Vous pouvez définir une table ayant la propriété IDENTITY lorsque vous créez la table à l’aide d’une syntaxe similaire à l’instruction suivante :

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Vous pouvez ensuite utiliser `INSERT..SELECT` pour remplir la table.

This remainder of this section highlights the nuances of the implementation to help you understand them more fully.  

### <a name="allocation-of-values"></a>Allocation de valeurs
La propriété IDENTITY ne garantit pas l’ordre dans lequel les valeurs de substitution sont alloués, ce qui reflète le comportement de SQL Server et d’Azure SQL Database. Toutefois, dans Azure SQL Data Warehouse, l’absence de garantie est plus marquée. 

L’exemple suivant en est une illustration :

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Dans l’exemple précédent, les deux lignes sont arrivées dans la distribution 1. La première ligne a la valeur de substitution 1 dans la colonne `C1`, et la deuxième la valeur de substitution 61. Ces deux valeurs ont été générées par la propriété IDENTITY. Toutefois, l’allocation des valeurs n’est pas contiguë. Ce comportement est normal.

### <a name="skewed-data"></a>Données décalées 
La plage de valeurs pour le type de données est répartie uniformément entre les distributions. Si une table distribuée subit un décalage de données, la plage de valeurs disponibles pour le type de données peut être épuisée prématurément. Par exemple, si toutes les données se terminent dans une seule distribution, la table a accès à seulement un sixième des valeurs du type de données. Pour cette raison, la propriété IDENTITY se limite aux types de données `INT` et `BIGINT` uniquement.

### <a name="selectinto"></a>SELECT .. INTO
Lorsqu’une colonne IDENTITY existante est sélectionnée dans une nouvelle table, la nouvelle colonne hérite de la propriété IDENTITY, sauf si une des conditions suivantes est remplie :
- L’instruction SELECT contient une jointure.
- Plusieurs instructions SELECT sont jointes à l’aide d’UNION.
- La colonne IDENTITY est répertoriée plusieurs fois dans la liste SELECT.
- La colonne SELECT fait partie d’une expression.
    
Si l’une de ces conditions est vraie, la colonne est créée comme étant NOT NULL au lieu d’hériter de la propriété IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) suit le même comportement SQL Server que celui documenté pour SELECT..INTO. Toutefois, vous ne pouvez pas spécifier de propriété IDENTITY dans la définition de la colonne de la partie `CREATE TABLE` de l’instruction. Vous ne pouvez pas non plus utiliser la fonction IDENTITY dans la partie `SELECT` de l’instruction CTAS. Pour remplir une table, vous devez utiliser `CREATE TABLE` pour définir la table suivie de l’instruction `INSERT..SELECT`.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Explicitly inserting values into an IDENTITY column 
SQL Data Warehouse prend en charge la syntaxe `SET IDENTITY_INSERT <your table> ON|OFF`. Vous pouvez utiliser cette syntaxe pour insérer explicitement des valeurs dans la colonne IDENTITY.

Nombreux sont les modélisateurs de données à aimer utiliser des valeurs négatives prédéfinies pour certaines lignes dans leurs dimensions. Un exemple est la ligne -1 ou « membre inconnu ». 

Le script suivant montre comment ajouter explicitement cette ligne à l’aide de SET IDENTITY_INSERT :

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="loading-data"></a>Chargement des données

La présence de la propriété IDENTITY a des conséquences sur votre code de chargement des données. Cette section met en évidence certains modèles de base pour charger des données dans les tables à l’aide d’IDENTITY. 

Pour charger des données dans une table et générer une clé de substitution à l’aide d’IDENTITY, créez la table et utilisez l’instruction INSERT... SELECT ou INSERT... VALEURS pour effectuer le chargement.

L’exemple suivant met en évidence le modèle de base :
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> It's not possible to use CREATE TABLE AS SELEC` currently when loading data into a table with an IDENTITY column.
> 

For more information on loading data, see <bpt id="p1">[</bpt>Designing Extract, Load, and Transform (ELT) for Azure SQL Data Warehouse<ept id="p1">](design-elt-data-loading.md)</ept> and  <bpt id="p2">[</bpt>Loading best practices<ept id="p2">](guidance-for-loading-data.md)</ept>.


## <a name="system-views"></a>Vues système
You can use the <bpt id="p1">[</bpt>sys.identity_columns<ept id="p1">](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql)</ept> catalog view to identify a column that has the IDENTITY property.

To help you better understand the database schema, this example shows how to integrate sys.identity_column` with other system catalog views:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limites
The IDENTITY property can't be used:
- When the column data type is not INT or BIGINT
- When the column is also the distribution key
- When the table is an external table 

Les fonctions associées suivantes ne sont pas prises en charge dans SQL Data Warehouse :

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Tâches courantes

Cette section fournit un exemple de code que vous pouvez utiliser pour effectuer des tâches courantes, lorsque vous travaillez avec des colonnes IDENTITY. 

La colonne C1 est la colonne IDENTITY dans toutes les tâches suivantes.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Rechercher la valeur allouée la plus élevée d’une table
Utilisez la fonction `MAX()` pour déterminer la valeur maximale allouée d’une table distribuée :

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Rechercher la valeur initiale et l’incrément de la propriété IDENTITY
Vous pouvez utiliser les affichages catalogue pour découvrir les valeurs de configuration de la valeur initiale et de l’incrément d’identité d’une table à l’aide de la requête suivante : 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Étapes suivantes

* To learn more about developing tables, see the [Table overview][Overview].  

