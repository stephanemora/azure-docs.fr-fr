---
title: Instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse | Microsoft Docs
description: Conseils de codage à l’aide de l’instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse pour le développement de solutions.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: igorstan
ms.openlocfilehash: f791f460efec1b84533379e74add003619dbac6f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521565"
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Utilisation de l’instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse
Conseils de codage à l’aide de l’instruction T-SQL CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse pour le développement de solutions.

## <a name="what-is-create-table-as-select-ctas"></a>Qu’est-ce que CREATE TABLE AS SELECT (CTAS) ?

L’instruction [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ou CTAS est l’une des fonctionnalités T-SQL les plus importantes. Il s’agit d’une opération parallèle qui crée une table en fonction de la sortie d’une instruction SELECT. CTAS est le moyen le plus rapide pour créer et insérer des données dans une table avec une seule commande. 

## <a name="selectinto-vs-ctas"></a>SELECT..INTO et CTAS
Vous pouvez considérer CTAS comme une version améliorée de l’instruction [SELECT...INTO](/sql/t-sql/queries/select-into-clause-transact-sql).

Voici un exemple simple d’instruction SELECT...INTO :

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Toutefois, SELECT...INTO ne vous permet pas de modifier la méthode de distribution ni le type d’index dans le cadre de l’opération. `[dbo].[FactInternetSales_new]` est créé avec le type de distribution par défaut en tant que ROUND_ROBIN et la structure de table par défaut en tant qu’INDEX cluster COLUMNSTORE.

Utilisation de CTAS, vous êtes en mesure de spécifier la distribution de données de la table, ainsi que le type de structure de table.
Pour convertir l’exemple précédent en CTAS :

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

 

> [!NOTE]
> Si vous essayez uniquement de modifier l’index dans votre opération `CTAS` et que la table source est distribuée par hachage, vous pouvez optimiser les performances de votre opération `CTAS` en conservant la même colonne de distribution et le même type de données. Ceci permet d’éviter le déplacement des données entre les distributions pendant l’opération, d’où une efficacité accrue.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Utilisation de CTAS pour copier une table
L’une des utilisations les plus courantes de `CTAS` consiste sans doute à créer une copie d’une table afin que vous puissiez modifier la DDL. Si, par exemple, vous avez créé votre table en tant que `ROUND_ROBIN` et que vous souhaitez maintenant Remplacez-la par une table distribuée sur une colonne, `CTAS` est la façon de modifier la colonne de distribution. `CTAS` peut également servir à changer le partitionnement, l’indexation ou les types de colonnes.

Supposons que vous avez créé cette table en utilisant le type de distribution par défaut `ROUND_ROBIN` distribué, car aucune colonne de distribution n’a été spécifiée dans `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Maintenant, vous souhaitez créer une copie de cette table avec un index Columnstore en cluster afin de tirer parti des performances de tables Columnstore en cluster. Vous souhaitez également distribuer cette table sur ProductKey dans la mesure où vous anticipez des jointures sur cette colonne et vous souhaitez éviter le déplacement de données au cours des jointures sur ProductKey. Enfin, vous souhaitez également ajouter le partitionnement sur OrderDateKey pour supprimer rapidement les anciennes données en supprimant les anciennes partitions. Voici l’instruction CTAS, permettant de copie l’ancienne table dans une nouvelle table.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Pour finir, vous pouvez renommer vos tables pour intégrer votre nouvelle table et supprimer votre ancienne table.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>Utilisation de CTAS pour contourner les fonctionnalités non prises en charge
CTAS permet également de contourner un certain nombre de fonctionnalités non prises en charge répertoriées ci-après. Cette méthode peut souvent s’avérer un doublement avantageuse win, car non seulement votre code sera conforme, mais il est souvent exécuter plus rapidement sur SQL Data Warehouse. Ces performances est un résultat de sa conception entièrement parallélisée. Les scénarios qui peuvent être contournés avec CTAS comprennent notamment :

* Jointures ANSI sur les opérations UPDATE
* Jointures ANSI sur les opérations DELETE
* Instruction MERGE

> [!NOTE]
> Envisagez toujours de recourir à l’instruction CTAS en premier lieu. Si vous pensez pouvoir résoudre un problème en utilisant une instruction `CTAS` , il s’agit généralement de la meilleure approche à adopter, même si cela vous oblige à écrire davantage de données.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>Remplacement de jointures ANSI pour les instructions de mise à jour
Vous disposez peut-être d’une mise à jour complexe qui joint plus de deux tables à l’aide d’une syntaxe de jointure ANSI pour l’exécution d’une opération UPDATE ou DELETE.

Imaginons que vous souhaitiez mettre à jour la table suivante :

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

La requête d’origine peut se présenter comme suit :

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Étant donné que SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la clause `FROM` d’une instruction `UPDATE`, vous ne pouvez pas copier ce code sans le modifier légèrement.

Vous pouvez remplacer ce code en combinant une instruction `CTAS` et une jointure implicite :

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Remplacement de jointures ANSI pour les instructions de suppression
L’utilisation de l’instruction `CTAS`constitue parfois la meilleure approche pour supprimer des données. Au lieu de supprimer les données, sélectionnez les données que vous souhaitez conserver. Cela est particulièrement vrai pour `DELETE` instructions qui utilisent ANSI syntaxes de jointure dans la mesure où SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la `FROM` clause d’une `DELETE` instruction.

Voici un exemple d’instruction DELETE convertie :

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Remplacement d’instructions MERGE
Vous pouvez remplacer les instructions MERGE, du moins partiellement, à l’aide de CTAS. Vous pouvez consolider les instructions INSERT et UPDATE dans une seule et même instruction. Tous les enregistrements supprimés doivent être limités à partir de la `SELECT` instruction à omettre dans les résultats.

L’exemple suivant est pour une opération upsert simple :

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recommandation CTAS : déclarer explicitement le type de données et la possibilité de valeur NULL de la sortie
Lorsque vous migrez des code, vous constaterez que vous allez exécuter sur ce type de modèle de codage :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinctivement, vous pourriez envisager de convertir ce code en instruction CTAS, et vous auriez raison. Toutefois, un problème se dissimule derrière ce scénario.

Le code suivant ne génère PAS le même résultat :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Notez que la colonne « result » indique le type de données et les valeurs de possibilité de valeur NULL de l’expression. De légers écarts de valeurs peuvent en découler si vous n’y prenez pas garde.

Essayez l’exemple de code suivant :

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

La valeur stockée en guise de résultat diffère. Comme la valeur persistante dans la colonne de résultats est utilisée dans d’autres expressions, l’erreur devient encore plus significative.

![Résultats CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ceci se révèle particulièrement important dans le cas des migrations de données. Bien que la seconde requête soit effectivement plus précise, il existe un problème. Les données obtenues diffèrent de celles du système source, ce qui compromet l’intégrité de la migration. Il s’agit là de l’un des rares cas dans lesquels la « mauvaise » réponse est en réalité la bonne !

Cet écart entre les deux résultats est dû à la conversion de type (transtypage) implicite. Dans le premier exemple, le tableau définit la définition de colonne. Lorsque la ligne est insérée, une conversion de type implicite se produit. Dans le deuxième exemple, il n’existe aucune conversion de type implicite car l’expression définit le type de données de la colonne. Notez également que la colonne figurant dans le second exemple a été définie comme une colonne Nullable, ce qui n’est pas son cas dans le premier exemple. Lorsque la table a été créée dans le premier exemple, les valeurs de colonne NULL a été définie explicitement. Dans le deuxième exemple, il se trouvait à l’expression et, par défaut, entraînerait une définition NULL.  

Pour résoudre ces problèmes, vous devez définir explicitement la conversion de type et la possibilité de valeur NULL dans la partie SELECT de l’instruction CTAS. Vous ne pouvez pas définir ces propriétés dans la partie CREATE TABLE.

L’exemple ci-dessous indique comme corriger le code :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Notez les points suivants :

* Nous aurions pu utiliser CAST ou CONVERT.
* La possibilité de valeur Null est forcée avec ISNULL, et non avec COALESCE.
* ISNULL est la fonction située la plus à l’extérieur.
* La seconde partie de l’instruction ISNULL est une constante, c’est-à-dire 0.

> [!NOTE]
> La possibilité de valeur Null doit être impérativement définie avec ISNULL, et non avec COALESCE. COALESCE n’est pas une fonction déterministe et par conséquent, le résultat de l’expression sera toujours NULLable. ISNULL est différente. Cette fonction est déterministe. Par conséquent, lorsque la seconde partie de la fonction ISNULL est une constante ou un littéral, la valeur résultante est NOT NULL.
> 
> 

Ce conseil ne vous sert pas seulement à assurer l’intégrité de vos calculs. Il est également important dans le cadre du basculement de partition de table. Imaginons que vous ayez défini la table suivante :

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Toutefois, le champ de valeur est une expression calculée qui ne fait pas partie des données sources.

Pour créer votre jeu de données partitionnée, vous souhaiterez peut-être effectuer les opérations suivantes :

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Cette requête s’exécute parfaitement. Toutefois, un problème se pose lorsque vous essayez de procéder au basculement de partition. Les définitions de table ne correspondent pas. Pour rendre les définitions de table correspondent, CTAS doit être modifiée pour ajouter un `ISNULL` (fonction) pour conserver l’attribut de possibilité de valeur null de la colonne.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Vous pouvez alors constater que la cohérence des types et le maintien des propriétés de possibilité de valeur Null sur une instruction CTAS constituent des approches d’ingénierie judicieuses. Ces opérations contribuent à garantir l’intégrité de vos calculs, ainsi que la possibilité de basculement de partition.

Reportez-vous à la [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) documentation. Cette instruction est l’une des plus importantes d’Azure SQL Data Warehouse. Vous devez donc faire en sorte d’en comprendre les moindres aspects.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).

