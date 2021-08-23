---
title: Ressources pour le développement de fonctionnalités SQL Synapse
description: Concepts de développement, choix de conception, recommandations et techniques de codage pour SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 17df008ccc5fcada0ad58313622fc997ccfa3f77
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532918"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Choix de conception et techniques de codage pour les fonctionnalités SQL Synapse dans Azure Synapse Analytics
Dans cet article, vous trouverez une liste de ressources pour le pool SQL dédié et les fonctions de pool SQL serverless de Synapse SQL. Les articles recommandés sont répartis dans deux sections : Principaux choix de conception et techniques de développement et de codage.

L'objectif de ces articles est de vous aider à développer l'approche technique optimale pour les composants Synapse SQL au sein d'Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Choix de conception clés
Les articles ci-dessous mettent en évidence les concepts et les choix de conception pour le développement SQL Synapse :

| Article | Pool SQL dédié | Pool SQL serverless |
| ------- | -------- | ------------- |
| [Connexions](connect-overview.md)                    | Oui | Oui |
| [Classes de ressources et concurrence](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context) | Oui    | Non |
| [Transactions](develop-transactions.md)              | Oui | Non |
| [Schémas définis par l’utilisateur](develop-user-defined-schemas.md) | Oui | Oui |
| [Distribution de table](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)                 | Oui | Non |
| [Index de table](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)                           | Oui | Non |
| [Partitions de table](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)                     | Oui | Non |
| [Statistiques](develop-tables-statistics.md)            | Oui | Oui |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)                                             | Oui | Non |
| [Tables externes](develop-tables-external-tables.md) | Oui | Oui |
| [CETAS](develop-tables-cetas.md)                     | Oui | Oui |


## <a name="recommendations"></a>Recommandations

Vous trouverez ci-dessous des articles essentiels qui mettent en évidence des techniques de codage, des conseils et des recommandations spécifiques pour le développement :

| Article | Pool SQL dédié | Pool SQL serverless |
| ------- | -------- | ------------- |
| [procédures stockées](develop-stored-procedures.md)  | Oui                | Oui                      |
| [Étiquettes](develop-label.md)                           | Oui                | Non                      |
| [Views](develop-views.md)                             | Oui                | Oui                     |
| [Tables temporaires](develop-tables-temporary.md)       | Oui                | Oui                     |
| [SQL dynamique](develop-dynamic-sql.md)                 | Oui                | Oui                     |
| [Bouclage](develop-loops.md)                         | Oui                | Oui                     |
| [Options de regroupement](develop-group-by-options.md)       | Oui                | Non                      |
| [Attribution de variables](develop-variable-assignment.md) | Oui                | Oui                     |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Instructions T-SQL pour le pool SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?context=/azure/synapse-analytics/context/context).

