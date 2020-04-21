---
title: Ressources pour le développement de fonctionnalités SQL Synapse
description: Concepts de développement, choix de conception, recommandations et techniques de codage pour SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426765"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Choix de conception et techniques de codage pour les fonctionnalités SQL Synapse dans Azure Synapse Analytics
Cet article contient une liste de ressources pour le pool SQL et des fonctions SQL à la demande (préversion) de SQL Synapse. Les articles recommandés sont répartis dans deux sections : Principaux choix de conception et techniques de développement et de codage.

Ces articles ont pour but de vous aider à développer l’approche technique optimale pour les composants SQL Synapse à l’intérieur de Synapse Analytics.

## <a name="key-design-decisions"></a>Choix de conception clés
Les articles ci-dessous mettent en évidence les concepts et les choix de conception pour le développement SQL Synapse :

|                                                          |   Pool SQL   | SQL à la demande |
| -----------------------------------------------------    | ---- | ---- |
| [Connexions](connect-overview.md)                    | Oui | Oui |
| [Classes de ressources et concurrence](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Oui    | Non |
| [Transactions](develop-transactions.md)              | Oui | Non |
| [Schémas définis par l’utilisateur](develop-user-defined-schemas.md) | Oui | Oui |
| [Distribution de table](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Oui | Non |
| [Index de table](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Oui | Non |
| [Partitions de table](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Oui | Non |
| [Statistiques](develop-tables-statistics.md)            | Oui | Oui |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Oui | Non |
| [Tables externes](develop-tables-external-tables.md) | Oui | Oui |
| [CETAS](develop-tables-cetas.md)                     | Oui | Oui |


## <a name="recommendations"></a>Recommandations

Vous trouverez ci-dessous des articles essentiels qui mettent en évidence des techniques de codage, des conseils et des recommandations spécifiques pour le développement :

|                                            | Pool SQL | SQL à la demande |
| ------------------------------------------ | ------------------ | ----------------------- |
| [procédures stockées](develop-stored-procedures.md)  | Oui                | Non                      |
| [Étiquettes](develop-label.md)                           | Oui                | Non                      |
| [Views](develop-views.md)                             | Oui                | Oui                     |
| [Tables temporaires](develop-tables-temporary.md)       | Oui                | Oui                     |
| [SQL dynamique](develop-dynamic-sql.md)                 | Oui                | Oui                     |
| [Bouclage](develop-loops.md)                         | Oui                | Oui                     |
| [Options de regroupement](develop-group-by-options.md)       | Oui                | Non                      |
| [Attribution de variables](develop-variable-assignment.md) | Oui                | Oui                     |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Instructions T-SQL pour le pool SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

