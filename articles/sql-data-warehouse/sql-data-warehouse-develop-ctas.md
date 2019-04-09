---
title: Instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse | Microsoft Docs
description: Explication et exemples d’avec l’instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse pour développer des solutions.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: ea95a13277927b485bb9da3b75b84cce4337bf88
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280432"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE AS SELECT (CTAS) dans SQL Azure Data Warehouse

Explication et exemples de codage avec l’instruction CREATE TABLE AS SELECT (CTAS) T-SQL dans Azure SQL Data Warehouse pour le développement de solutions.

## <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

L’instruction [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ou CTAS est l’une des fonctionnalités T-SQL les plus importantes. CTAS est une opération parallèle qui crée une table basée sur la sortie d’une instruction SELECT. CTAS est le moyen le plus rapide pour créer et insérer des données dans une table avec une seule commande.

## <a name="selectinto-vs-ctas"></a>SELECT..INTO et CTAS

CTAS est une version améliorée de la [sélectionnez... DANS](/sql/t-sql/queries/select-into-clause-transact-sql) instruction.

Voici un exemple simple d’instruction SELECT...INTO :

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SÉLECTIONNEZ... DANS n’autorise pas de modifier la méthode de distribution ou le type d’index dans le cadre de l’opération. `[dbo].[FactInternetSales_new]` est créé avec le type de distribution par défaut de ROUND_ROBIN et la structure de table par défaut en tant qu’INDEX cluster COLUMNSTORE.

Type de la distribution des données de la table, ainsi que la structure de table à l’aide de CTAS vous êtes en mesure de spécifier à la fois. Pour convertir l’exemple précédent en CTAS :

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Si vous essayez uniquement de modifier l’index dans votre opération `CTAS` et que la table source est distribuée par hachage, vous pouvez optimiser les performances de votre opération `CTAS` en conservant la même colonne de distribution et le même type de données. Ceci permet d’éviter le déplacement des données entre les distributions pendant l’opération, d’où une efficacité accrue.

## <a name="using-ctas-to-copy-a-table"></a>Utilisation de CTAS pour copier une table

Par exemple le plus souvent utilisé pour `CTAS` crée une copie d’une table afin de modifier l’instruction DDL. Si, par exemple, vous avez créé votre table en tant que `ROUND_ROBIN` et que vous souhaitez maintenant Remplacez-la par une table distribuée sur une colonne, `CTAS` est la façon de modifier la colonne de distribution. `CTAS` peut également être utilisé pour modifier les types de partitionnement, l’indexation ou de colonne.

Supposons que vous avez créé cette table en utilisant le type de distribution par défaut de `ROUND_ROBIN` , ne spécifiez ne pas une colonne de distribution dans le `CREATE TABLE`.

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

Maintenant, vous souhaitez créer une copie de cette table avec un index Columnstore en cluster afin de tirer parti des performances de tables Columnstore en cluster. Vous souhaitez également distribuer cette table sur ProductKey dans la mesure où vous anticipez des jointures sur cette colonne et vous souhaitez éviter le déplacement de données au cours des jointures sur ProductKey. Enfin, vous souhaitez également ajouter le partitionnement à OrderDateKey afin que vous pouvez rapidement supprimer les anciennes données en éliminant les anciennes partitions. Voici l’instruction CTAS, permettant de copie l’ancienne table dans une nouvelle table.

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
> Envisagez toujours de recourir à l’instruction CTAS en premier lieu. Résoudre un problème à l’aide `CTAS` est généralement une bonne approche, même si vous écrivez ainsi davantage de données.

## <a name="ansi-join-replacement-for-update-statements"></a>Remplacement de jointures ANSI pour les instructions de mise à jour

Vous constaterez que vous disposez d’une mise à jour complexe qui joint plus de deux tables à l’aide de syntaxe de jointure ANSI pour effectuer la mise à jour ou suppression.

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

La requête d’origine peut se présenter comme dans cet exemple :

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

SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la `FROM` clause d’une `UPDATE` instruction, vous ne pouvez pas utiliser l’exemple précédent sans le modifier.

Vous pouvez utiliser une combinaison d’un `CTAS` et une jointure implicite pour remplacer l’exemple précédent :

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

Parfois, la meilleure approche pour la suppression des données consiste à utiliser `CTAS`, en particulier pour `DELETE` syntaxes de jointure des instructions qui utilisent ANSI. Il s’agit, car SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la `FROM` clause d’une `DELETE` instruction. Au lieu de supprimer les données, sélectionnez les données que vous souhaitez conserver.

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

Vous pouvez remplacer les instructions MERGE, du moins partiellement, à l’aide de CTAS. Vous pouvez combiner l’insertion et la mise à jour dans une instruction unique. Tous les enregistrements supprimés doivent être limités à partir de la `SELECT` instruction à omettre dans les résultats.

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

Instinctivement, vous pourriez envisager de convertir ce code en instruction CTAS, et vous auriez raison. Toutefois, il existe un problème caché.

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

Notez que la colonne « result » indique le type de données et les valeurs de possibilité de valeur NULL de l’expression. Type vers l’avant exécution les données peut entraîner de légers écarts dans les valeurs si vous ne faites pas attention.

Essayez l’exemple suivant :

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

Ceci est important pour les migrations de données. Bien que la deuxième requête est sans doute plus précise, il existe un problème. Les données obtenues diffèrent de celles du système source, ce qui compromet l’intégrité de la migration. Il s’agit là de l’un des rares cas dans lesquels la « mauvaise » réponse est en réalité la bonne !

La raison pour laquelle que nous voir une disparité entre les deux résultats est en raison de la conversion de type implicite. Dans le premier exemple, le tableau définit la définition de colonne. Lorsque la ligne est insérée, une conversion de type implicite se produit. Dans le deuxième exemple, il n’existe aucune conversion de type implicite car l’expression définit le type de données de la colonne. Notez également que la colonne figurant dans le second exemple a été définie comme une colonne Nullable, ce qui n’est pas son cas dans le premier exemple. Lorsque la table a été créée dans le premier exemple, les valeurs de colonne NULL a été définie explicitement. Dans le deuxième exemple, il se trouvait à l’expression et, par défaut, entraînerait une définition NULL.

Pour résoudre ces problèmes, vous devez définir explicitement la conversion de type et la possibilité de valeur NULL dans la partie SELECT de l’instruction CTAS. Vous ne pouvez pas définir ces propriétés dans la partie de la table create.

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
* La deuxième partie de ISNULL est une constante, 0

> [!NOTE]
> La possibilité de valeur Null doit être impérativement définie avec ISNULL, et non avec COALESCE. COALESCE n’est pas une fonction déterministe et par conséquent, le résultat de l’expression sera toujours NULLable. ISNULL est différente. Cette fonction est déterministe. Par conséquent, lorsque la seconde partie de la fonction ISNULL est une constante ou un littéral, la valeur résultante est NOT NULL.

Il est également important pour le basculement de partition de table d’assurer l’intégrité de vos calculs. Supposons que vous ayez défini comme une table de faits cette table :

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

Toutefois, le champ Quantité est une expression calculée ; Il ne fait pas partie de la source de données.

Pour créer votre jeu de données partitionnée, vous souhaiterez peut-être utiliser le code suivant :

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

Cette requête s’exécute parfaitement. Le problème se produit lorsque vous essayez d’effectuer le basculement de partition. Les définitions de table ne correspondent pas. Pour rendre les définitions de table correspondent, CTAS doit être modifiée pour ajouter un `ISNULL` (fonction) pour conserver l’attribut de possibilité de valeur null de la colonne.

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

Vous pouvez voir que la cohérence des types et maintien des propriétés de possibilité de valeur NULL dans une instruction CTAS est une bonne pratique d’ingénierie. Ces opérations contribuent à garantir l’intégrité de vos calculs, ainsi que la possibilité de basculement de partition.

Reportez-vous à la [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) documentation. CTAS est une des instructions plus importantes dans Azure SQL Data Warehouse. Vous devez donc faire en sorte d’en comprendre les moindres aspects.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](sql-data-warehouse-overview-develop.md).

