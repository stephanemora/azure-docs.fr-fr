---
title: Clés primaires, étrangères et uniques
description: Prise en charge des contraintes de table SQL Analytics dans Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b9336a5e230e90e1abd7f2d40d431b988385c009
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350031"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Clé primaire, clé étrangère et clé unique dans SQL Analytics

Découvrez les contraintes de table SQL Analytics, notamment la clé primaire, la clé étrangère et la clé unique.

## <a name="table-constraints"></a>Contraintes de table 
SQL Analytics prend en charge les contraintes de table suivantes : 
- La contrainte PRIMARY KEY est prise en charge seulement si NONCLUSTERED et NOT ENFORCED sont tous les deux utilisés.    
- La contrainte UNIQUE est prise en charge seulement si NOT ENFORCED est utilisé.   

La contrainte FOREIGN KEY n'est pas prise en charge dans SQL Analytics.  

## <a name="remarks"></a>Notes
La présence d'une clé primaire et/ou d'une clé unique permet au moteur de SQL Analytics de générer un plan d'exécution optimal pour une requête.  Toutes les valeurs d’une colonne de clé primaire ou d’une colonne de contrainte unique doivent être uniques. 

Après avoir créé une table avec contrainte de clé primaire ou unique dans SQL Analytics, les utilisateurs doivent s'assurer que toutes les valeurs de ces colonnes sont uniques.  Si cette condition n’est pas respectée, la requête risque de retourner un résultat inexact.  Cet exemple illustre le fait qu’une requête peut retourner un résultat inexact si la colonne de contrainte de clé primaire ou unique contient des valeurs en double.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Exemples
Création d'une table SQL Analytics avec clé primaire : 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Création d'une table SQL Analytics avec contrainte unique :

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé les tables dans votre base de données SQL Analytics, l'étape suivante consiste à charger des données dans ces tables. Pour suivre un tutoriel consacré au chargement, consultez [Chargement de données dans des bases de données SQL Analytics](load-data-wideworldimportersdw.md).
