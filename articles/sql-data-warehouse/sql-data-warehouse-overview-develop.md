---
title: Ressources pour le développement d’un entrepôt de données dans Azure | Microsoft Docs
description: Concepts de développement, choix de conception, recommandations et des techniques de codage pour SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 613bcb05dab993989a2ae00b71fef95794953ab8
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850732"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Choix de conception et techniques de codage pour SQL Data Warehouse
Consultez les articles sur le développement afin de mieux comprendre les choix de conception, les recommandations et les techniques de codage fondamentaux pour SQL Data Warehouse.

## <a name="key-design-decisions"></a>Choix de conception clés
Les articles suivants mettent l’accent sur les concepts et choix de conception pour le développement d’un entrepôt de données distribué à l’aide de SQL Data Warehouse :

* [Connexions][connections]
* [Accès concurrentiel][concurrency]
* [Transactions][transactions]
* [Schémas définis par l’utilisateur][user-defined schemas]
* [Distribution de tables][table distribution]
* [Index de table][table indexes]
* [Partitions de table][table partitions]
* [CTAS][CTAS]
* [Statistiques][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Recommandations pour le développement et techniques de codage
Ces articles mettent l’accent sur des techniques de codage spécifiques, des conseils et des recommandations pour le développement de SQL Data Warehouse :

* [Procédures stockées][stored procedures]
* [Étiquettes][labels]
* [Vues][views]
* [Tables temporaires][temporary tables]
* [SQL dynamique][dynamic SQL]
* [Bouclage][looping]
* [Options de regroupement][group by options]
* [Attribution de variables][variable assignment]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, voir [Instructions SQL dans SQL Data Warehouse](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
