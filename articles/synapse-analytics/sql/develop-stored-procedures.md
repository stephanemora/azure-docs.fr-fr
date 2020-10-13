---
title: Utiliser des procédures stockées
description: Conseils sur l’implémentation des procédures stockées dans Synapse SQL pour le développement de solutions.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 09/23/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f2046614f3665a699d02c76210676fb32f99fc73
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288917"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>Utiliser des procédures stockées dans Synapse SQL

Conseils relatifs à l’implémentation de procédures stockées dans un pool SQL Synapse à des fins de développement de solutions.

## <a name="what-to-expect"></a>À quoi s’attendre

Synapse SQL prend en charge plusieurs fonctionnalités T-SQL qui sont utilisées dans SQL Server. Plus important encore, il existe différentes fonctions, propres à l’augmentation de la taille des instances, que nous voulons exploiter pour optimiser les performances de notre solution.

Pour assurer la mise à l’échelle et les performances du pool SQL, il existe divers mécanismes et fonctions dont le comportement présente des différences, ainsi que d’autres qui ne sont pas pris en charge.

## <a name="stored-procedures-in-synapse-sql"></a>Procédures stockées dans Synapse SQL

Une procédure stockée est un excellent moyen d’encapsuler votre code SQL, en le stockant à un emplacement proche de vos données au sein de l’entrepôt de données. Les procédures stockées aident les développeurs à modulariser leurs solutions en encapsulant le code sous la forme d’unités pouvant être facilement gérées, afin d’optimiser la réutilisation du code. Chaque procédure stockée peut également accepter des paramètres, ce qui les rend encore plus flexibles. Dans l’exemple suivant, vous pouvez voir les procédures qui suppriment des objets externes s’ils se trouvent déjà dans la base de données :

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Ces procédures peuvent être exécutées à l’aide de l’instruction `EXEC`, dans laquelle vous pouvez spécifier le nom de la procédure et les paramètres :

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL fournit une implémentation simplifiée et rationalisée pour les procédures stockées. La plus grande différence par rapport à SQL Server est le fait que la procédure stockée ne correspond pas à du code précompilé. Dans les entrepôts de données, la durée de compilation est faible en comparaison au temps nécessaire pour exécuter des requêtes sur de grands volumes de données. Il est plus important de veiller à ce que le code de procédure stockée soit correctement optimisé pour les grosses requêtes. Le gain de temps recherché se compte en heures, en minutes et en secondes, et non en millisecondes. De ce fait, il est plus utile de considérer les procédures stockées comme des conteneurs de logique SQL.

Lorsque Synapse SQL exécute votre procédure stockée, les instructions SQL sont analysées, traduites et optimisées au moment de l’exécution. Lors de ce processus, chaque instruction est convertie en différentes requêtes distribuées. Le code SQL appliqué aux données est différent de la requête envoyée.

## <a name="encapsulate-validation-rules"></a>Encapsuler les règles de validation

Les procédures stockées vous permettent de localiser la logique de validation d’un module stocké dans la base de données SQL. Dans l’exemple suivant, vous allez voir comment valider les valeurs des paramètres et modifier leurs valeurs par défaut.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

La logique de la procédure SQL validera les paramètres d’entrée lorsque la procédure sera appelée.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Imbrication des procédures stockées

Lorsque les procédures stockées appellent d’autres procédures stockées ou exécutent un code SQL dynamique, la procédure stockée ou procédure d’appel de code centrale est considérée comme « imbriquée ».
Le code suivant montre un exemple de procédure imbriquée :

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Cette procédure accepte un paramètre qui représente un certain nom, puis appelle d’autres procédures pour supprimer les objets portant ce nom.
Un pool Synapse SQL prend en charge un maximum de huit niveaux d’imbrication. Cette fonctionnalité est légèrement différente de SQL Server. qui prend en charge 32 niveaux d’imbrication.

L’appel de procédure stockée de premier niveau correspond au niveau d’imbrication 1.

```sql
EXEC clean_up 'mytest'
```

Si la procédure stockée effectue également un autre appel EXEC, le niveau d’imbrication passe à 2.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Si la deuxième procédure exécute du code SQL dynamique, ce niveau monte à 3.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL ne prend pas en charge [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Vous devez suivre le niveau d’imbrication. Il est peu probable que vous dépassiez la limite des huit niveaux d’imbrication, mais si c’est le cas, vous devez revoir votre code pour ramener les niveaux d’imbrication dans cette limite.

## <a name="insertexecute"></a>INSERT... EXECUTE

Synapse SQL ne vous permet pas de consommer le jeu de résultats d’une procédure stockée avec une instruction INSERT. Pour cela, une autre méthode existe. Pour obtenir un exemple, consultez l’article sur les [tables temporaires](develop-tables-temporary.md).

## <a name="limitations"></a>Limites

Certains aspects des procédures stockées Transact-SQL ne sont pas implémentés dans Synapse SQL. C’est le cas des :

* Procédures stockées temporaires
* Procédures stockées numérotées
* Procédures stockées étendues
* Procédures stockées CLR
* Option de chiffrement
* Option de réplication
* Paramètres table
* Paramètres en lecture seule
* Paramètres par défaut (dans le pool provisionné)
* Contextes d’exécution
* Instruction RETURN

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires, consultez la [vue d’ensemble du développement](develop-overview.md).
