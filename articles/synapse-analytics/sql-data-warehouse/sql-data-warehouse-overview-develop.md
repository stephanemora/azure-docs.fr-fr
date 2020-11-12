---
title: Ressources à utiliser pour développer un pool SQL dédié dans Azure Synapse Analytics
description: Concepts de développement, choix de conception, recommandations et techniques de codage pour un pool SQL dédié dans Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f158655cd7aeba647480325966c7f0cfcf65b13c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322142"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Choix de conception et techniques de codage pour un pool SQL dédié dans Azure Synapse Analytics 

 Cet article fournit des ressources supplémentaires qui vous aideront à mieux comprendre les principaux choix de conception, les recommandations et les techniques de codage relatifs à un pool SQL dédié dans Azure Synapse.

## <a name="key-design-decisions"></a>Choix de conception clés

Les articles suivants mettent en évidence les concepts et choix de conception relatifs au développement d’un entrepôt de données distribué à l’aide de la capacité de pool SQL dédié dans Azure Synapse :

* [connexions](../sql/connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [transactions](sql-data-warehouse-develop-transactions.md)
* [schémas définis par l’utilisateur](sql-data-warehouse-develop-user-defined-schemas.md)
* [Distribution de tables](sql-data-warehouse-tables-distribute.md)
* [Index de table](sql-data-warehouse-tables-index.md)
* [partitions de table](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statistiques](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recommandations pour le développement et techniques de codage

Les articles suivants présentent des techniques de codage, des conseils et des recommandations spécifiques pour le développement d’un pool SQL dédié :

* [procédures stockées](sql-data-warehouse-develop-stored-procedures.md)
* [étiquettes](sql-data-warehouse-develop-label.md)
* [vues](performance-tuning-materialized-views.md)
* [tables temporaires](sql-data-warehouse-tables-temporary.md)
* [SQL dynamique](sql-data-warehouse-develop-dynamic-sql.md)
* [bouclage](sql-data-warehouse-develop-loops.md)
* [regrouper par options](sql-data-warehouse-develop-group-by-options.md)
* [attribution de variables](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez [Instructions T-SQL](sql-data-warehouse-reference-tsql-statements.md).
