---
title: Présentation des principales différences de Machine Learning Services (avec R) dans Azure SQL Database (préversion)
description: Cette rubrique décrit les principales différences entre Azure SQL Database Machine Learning Services (avec R) et SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: 4350fb0e75f140e120ba6cd2f074ffa1816a8fce
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237482"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Principales différences entre Machine Learning Services dans Azure SQL Database et Machine Learning Services dans SQL Server

Le fonctionnement de Machine Learning Services (avec R) dans Azure SQL Database est similaire à celui de [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Voici leurs principales différences.

## <a name="language-support"></a>Support multilingue

SQL Server prend en charge R et Python grâce à [Extensibility Framework](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework), tandis que SQL Database ne gère pas les deux langages :

- R est le seul langage pris en charge dans SQL Database. Il n’existe aucune prise en charge de Python pour l’instant.
- La version de R est 3.4.4.
- Il n’est pas nécessaire de configurer `external scripts enabled` avec `sp_configure`. Après [inscription](sql-database-machine-learning-services-overview.md#signup), le Machine Learning est activé pour la base de données SQL concernée.

## <a name="package-management"></a>Gestion des packages

L’installation et la gestion des packages R fonctionnent différemment pour SQL Database et pour SQL Server :

- Les packages R s’installent avec [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Les packages ne peuvent pas effectuer d’appels réseau sortants. Cette limitation est similaire aux [règles de pare-feu par défaut de Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) dans SQL Server, mais elle n’est pas modifiable dans SQL Database.
- Les packages qui dépendent de runtimes externes (comme Java) ou dont l’installation ou l’utilisation impliquent un accès à des API de système d’exploitation ne sont pas pris en charge.

## <a name="resource-governance"></a>Gouvernance des ressources

Il n’est pas possible de limiter les ressources R avec [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) et les listes de ressources externes. Les ressources R correspondent à une partie des ressources SQL Database, qui dépend du niveau de service choisi. Pour plus d’informations, voir [Modèles d’achat Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Isolation à des fins de sécurité

Dans Azure SQL Database, la couche SQLPAL (SQL Platform Abstraction Layer) assure l’isolation des processus externes, ce qui permet de renforcer la sécurité de l’exécution des scripts R.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales, voir la documentation [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics).
- Pour apprendre à utiliser Machine Learning Services (avec R) dans Azure SQL Database, voir [Guide de démarrage rapide](sql-database-connect-query-r.md).
- Pour plus d’informations, voir [Tutoriels du langage R avec SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials).