---
title: Ressources à utiliser pour développer un entrepôt de données dans Azure Synapse Analytics
description: Concepts de développement, choix de conception, recommandations et des techniques de codage pour SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153313"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Choix de conception et techniques de codage pour un entrepôt de données dans Azure Synapse Analytics 
 Dans cet article, vous trouverez des ressources supplémentaires qui vous aideront à mieux comprendre les principaux choix de conception, recommandations et techniques de codage relatifs à un entrepôt de données dans Azure Synapse.

## <a name="key-design-decisions"></a>Choix de conception clés
Les articles suivants mettent en évidence les concepts et choix de conception relatifs au développement d'un entrepôt de données distribué à l'aide de la fonctionnalité SQL Analytics d'Azure Synapse :

* [connexions](sql-data-warehouse-connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [transactions](sql-data-warehouse-develop-transactions.md)
* [schémas définis par l’utilisateur](sql-data-warehouse-develop-user-defined-schemas.md)
* [Distribution de tables](sql-data-warehouse-tables-distribute.md)
* [Index de table](sql-data-warehouse-tables-index.md)
* [partitions de table](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statistiques](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recommandations pour le développement et techniques de codage
Les articles suivants présentent des techniques de codage spécifiques, des conseils et des recommandations sur le développement d'un entrepôt de données avec SQL Analytics :

* [procédures stockées](sql-data-warehouse-develop-stored-procedures.md)
* [étiquettes](sql-data-warehouse-develop-label.md)
* [vues](sql-data-warehouse-develop-views.md)
* [tables temporaires](sql-data-warehouse-tables-temporary.md)
* [SQL dynamique](sql-data-warehouse-develop-dynamic-sql.md)
* [bouclage](sql-data-warehouse-develop-loops.md)
* [regrouper par options](sql-data-warehouse-develop-group-by-options.md)
* [attribution de variables](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez [Instructions T-SQL](sql-data-warehouse-reference-tsql-statements.md).
