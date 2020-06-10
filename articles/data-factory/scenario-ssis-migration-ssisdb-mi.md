---
title: Migration SSIS avec Azure SQL Managed Instance en tant que destination des charges de travail de base de données
description: Migration SSIS avec Azure SQL Managed Instance en tant que destination des charges de travail de base de données.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116311"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migration SSIS avec Azure SQL Managed Instance en tant que destination des charges de travail de base de données

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Lorsque vous procédez à la migration de charges de travail de base de données d’une instance SQL Server vers Azure SQL Managed Instance, vous devez être familiarisé avec [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) (DMS) et les [topologies de réseau pour les migrations SQL Managed Instance avec DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Cet article traite de la migration de packages SSIS (SQL Server Integration Service) stockés dans le catalogue SSIS (SSISDB) et des travaux de SQL Server Agent qui planifient les exécutions des packages SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrer le catalogue SSIS (SSISDB)

La migration SSISDB peut être effectuée à l’aide de DMS comme décrit dans l’article : [Migrer des packages SSIS vers SQL Managed Instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Travaux SSIS vers l'agent SQL Managed Instance

SQL Managed Instance présente un planificateur natif de première classe tout comme SQL Server Agent en local.  Aucun outil de migration n’est encore disponible pour les travaux SSIS. Ils doivent donc être migrés de SQL Server Agent en local vers l’agent SQL Managed Instance par le biais de scripts ou d’une copie manuelle.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologies réseau pour les migrations SQL Managed Instance avec DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrer des packages SSIS vers une instance SQL Managed Instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Étapes suivantes

- [Se connecter à SSISDB dans Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Exécuter des packages SSIS déployés sur Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
