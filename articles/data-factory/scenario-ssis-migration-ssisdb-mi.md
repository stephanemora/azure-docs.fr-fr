---
title: Migration SSIS avec une instance gérée Azure SQL Database en tant que destination des charges de travail de base de données
description: Migration SSIS avec une instance gérée Azure SQL Database en tant que destination des charges de travail de base de données
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
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419049"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migration SSIS avec une instance gérée Azure SQL Database en tant que destination des charges de travail de base de données

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Quand vous procédez à la migration de charges de travail de base de données d’un serveur SQL local vers une instance managée Azure SQL Database, vous devez être familiarisé avec [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) (DMS) et les [topologies de réseau pour les migrations d’instances managées Azure SQL Database avec DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Cet article traite de la migration de packages SSIS (SQL Server Integration Service) stockés dans le catalogue SSIS (SSISDB) et des travaux de SQL Server Agent qui planifient les exécutions des packages SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrer le catalogue SSIS (SSISDB)

La migration SSISDB peut être effectuée à l’aide de DMS comme décrit dans l’article : [Faire migrer des packages SSIS vers une instance managée d’Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Migration de travaux SSIS vers un agent d’instance managée Azure SQL Database

Une instance managée Azure SQL Database présente un planificateur natif de première classe tout comme SQL Server Agent en local.  Aucun outil de migration n’est encore disponible pour les travaux SSIS. Ils doivent donc être migrés de SQL Server Agent en local vers l’agent d’instance managée Azure SQL Database par le biais de scripts ou d’une copie manuelle.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologies de réseau pour des migrations d’instances managées de bases de données SQL Azure à l’aide d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrer les packages SQL Server Integration Services vers une instance managée Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Étapes suivantes

- [Se connecter à SSISDB dans Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Exécuter des packages SSIS déployés sur Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
