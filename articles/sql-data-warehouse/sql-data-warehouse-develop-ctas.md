---
title: Instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse | Microsoft Docs
description: Explication et exemples de l’instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse pour développer des solutions.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 91de474cc0610099b4264cc6d0dfbd26e8df0618
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851446"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE AS SELECT (CTAS) dans SQL Azure Data Warehouse

Cet article explique l’instruction CREATE TABLE AS SELECT (CTAS) T-SQL dans Azure SQL Data Warehouse pour développer des solutions. L’article fournit également des exemples de code.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Le [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instruction (CTAS) est une des fonctionnalités de T-SQL plus importantes. CTAS est une opération parallèle qui crée une table basée sur la sortie d’une instruction SELECT. CTAS est le moyen le plus rapide pour créer et insérer des données dans une table avec une seule commande.

## <a name="selectinto-vs-ctas"></a>SÉLECTIONNEZ... DANS Visual Studio. CTAS

CTAS est une version plus personnalisable de la [sélectionnez... DANS](/sql/t-sql/queries/select-into-clause-transact-sql) instruction.

Voici un exemple d’une instruction SELECT simple... DANS :

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SÉLECTIONNEZ... EN ne vous autorise à modifier la méthode de distribution ou le type d’index dans le cadre de l’opération. Vous créez `[dbo].[FactInternetSales_new]` en utilisant le type de distribution par défaut de ROUND_ROBIN et la structure de table par défaut de l’INDEX cluster COLUMNSTORE.

En revanche, avec CTAS, vous pouvez spécifier la distribution de données de la table, ainsi que le type de structure de table. Pour convertir l’exemple précédent en CTAS :

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
> Si vous essayez uniquement de modifier l’index dans votre opération CTAS, et la table source est distribuée par hachage, mettre à jour le type de colonne et des données même distribution. Cela évite le déplacement des données de distribution entre pendant l’opération, qui est plus efficace.

## <a name="use-ctas-to-copy-a-table"></a>Utiliser CTAS pour copier une table

Peut-être une des utilisations plus courantes de CTAS crée une copie d’une table afin de modifier l’instruction DDL. Nous allons dire que vous avez créé votre table en tant que `ROUND_ROBIN`et que vous souhaitez maintenant Remplacez-la par une table distribuée sur une colonne. CTAS est la façon de modifier la colonne de distribution. Vous pouvez également utiliser CTAS pour modifier les types de partitionnement, l’indexation ou de colonne.

Nous allons dire que vous avez créé cette table en utilisant le type de distribution par défaut de `ROUND_ROBIN`, ne pas spécifier une colonne de distribution dans le `CREATE TABLE`.

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

Maintenant vous souhaitez créer une copie de cette table, avec un `Clustered Columnstore Index`, de sorte que vous pouvez tirer parti des performances des tables Columnstore en cluster. Vous souhaitez également distribuer cette table sur `ProductKey`, car vous êtes anticipation des jointures sur cette colonne et que vous souhaitez éviter le déplacement des données au cours des jointures sur `ProductKey`. Enfin, vous souhaitez également ajouter le partitionnement sur `OrderDateKey`, de sorte que vous pouvez rapidement supprimer les anciennes données en éliminant les anciennes partitions. Voici l’instruction CTAS, qui copie l’ancienne table dans une nouvelle table.

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

Enfin, vous pouvez renommer vos tables, pour échanger dans votre nouvelle table et ensuite supprimer l’ancienne table.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Utiliser CTAS pour contourner les fonctionnalités non prises en charge

Vous pouvez également utiliser CTAS pour contourner un certain nombre de fonctionnalités non prises en charge répertoriées ci-dessous. Cette méthode peut souvent s’avérer utile, car non seulement votre code sera conforme, mais il s’exécute souvent plus rapidement sur SQL Data Warehouse. Ces performances est un résultat de sa conception entièrement parallélisée. Scénarios :

* Jointures ANSI sur les opérations UPDATE
* Jointures ANSI sur les opérations DELETE
* Instruction MERGE

> [!TIP]
> Essayez de vous demander « CTAS premier. » Résolution d’un problème à l’aide de CTAS est généralement une bonne approche, même si vous écrivez ainsi davantage de données.

## <a name="ansi-join-replacement-for-update-statements"></a>Remplacement de jointures ANSI pour les instructions de mise à jour

Vous constaterez peut-être que vous disposez d’une mise à jour complexe. La mise à jour des jointures plus de deux tables ensemble pour effectuer la mise à jour ou suppression à l’aide de la syntaxe de jointure ANSI.

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

Vous pouvez utiliser une combinaison de CTAS et une jointure implicite pour remplacer l’exemple précédent :

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

Parfois, la meilleure approche pour la suppression des données consiste à utiliser CTAS, en particulier pour `DELETE` syntaxes de jointure des instructions qui utilisent ANSI. Il s’agit, car SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la `FROM` clause d’une `DELETE` instruction. Au lieu de supprimer les données, sélectionnez les données que vous souhaitez conserver.

Voici un exemple d’un texte converti `DELETE` instruction :

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
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

Vous pouvez remplacer les instructions merge, au moins en partie, à l’aide de CTAS. Vous pouvez combiner la `INSERT` et `UPDATE` dans une instruction unique. Tous les enregistrements supprimés doivent être limités à partir de la `SELECT` instruction à omettre dans les résultats.

L’exemple suivant concerne un `UPSERT`:

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

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>déclarer explicitement le type de données et la possibilité de valeur NULL de la sortie

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

Vous pourriez penser vous devez migrer ce code vers CTAS, et vous auriez raison. Toutefois, il existe un problème caché.

Le code suivant ne génère le même résultat :

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

![Résultats de la capture d’écran de CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ceci est important pour les migrations de données. Bien que la deuxième requête est sans doute plus précise, il existe un problème. Les données sont différentes par rapport au système source, et qui soulève la question de l’intégrité de la migration. Il s’agit là de l’un des rares cas dans lesquels la « mauvaise » réponse est en réalité la bonne !

La raison pour laquelle que nous voir une disparité entre les deux résultats est en raison de la conversion de type implicite. Dans le premier exemple, le tableau définit la définition de colonne. Lorsque la ligne est insérée, une conversion de type implicite se produit. Dans le deuxième exemple, il n’existe aucune conversion de type implicite car l’expression définit le type de données de la colonne.

Notez également que la colonne dans le deuxième exemple a été définie comme une colonne NULLable, tandis que dans le premier exemple il n’a pas. Lorsque la table a été créée dans le premier exemple, les valeurs de colonne NULL a été définie explicitement. Dans le deuxième exemple, il se trouvait à l’expression et par défaut entraînerait une définition NULL.

Pour résoudre ces problèmes, vous devez définir explicitement la conversion de type et la possibilité de valeur NULL dans la partie SELECT de l’instruction CTAS. Vous ne pouvez pas définir ces propriétés dans « CREATE TABLE ».
L’exemple suivant montre comment corriger le code :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Notez les points suivants :

* Vous pouvez utiliser CAST ou convertir.
* Utilisation de ISNULL, COALESCE pas, pour forcer la possibilité de valeur null. Consultez la Remarque ci-dessous.
* ISNULL est la fonction extérieure.
* La deuxième partie de ISNULL est une constante, 0.

> [!NOTE]
> La possibilité de valeur NULL doit être correctement défini, vous devez impérativement utiliser ISNULL et COALESCE pas. COALESCE n’est pas une fonction déterministe, et par conséquent, le résultat de l’expression sera toujours NULLable. ISNULL est différente. Elle est déterministe. Par conséquent, lors de la deuxième partie de la fonction ISNULL est une constante ou un littéral, la valeur résultante sera pas NULL.

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

Toutefois, le champ Quantité est une expression calculée. Il ne fait pas partie de la source de données.

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

La requête s’exécuterait parfaitement bien. Le problème se produit lorsque vous essayez d’effectuer le basculement de partition. Les définitions de table ne correspondent pas. Pour rendre les définitions de table correspondent, modifier le CTAS pour ajouter un `ISNULL` (fonction) pour conserver l’attribut de possibilité de valeur null de la colonne.

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

Vous pouvez voir que la cohérence des types et maintien des propriétés de possibilité de valeur NULL dans une instruction CTAS est une pratique d’ingénierie. Il vous aide à maintenir l’intégrité de vos calculs et garantit également que le basculement de partition est possible.

CTAS est une des instructions plus importantes dans SQL Data Warehouse. Vous devez donc faire en sorte d’en comprendre les moindres aspects. Consultez le [documentation de CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires sur le développement, consultez la [vue d’ensemble sur le développement](sql-data-warehouse-overview-develop.md).

