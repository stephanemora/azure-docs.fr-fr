---
title: Migrer des charges de travail SSIS locales vers SSIS dans Azure Data Factory
description: Migrez des charges de travail SSIS locales vers SSIS dans ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 52629b8e2e190cc041116e6f65488480712baf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929802"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrer des charges de travail SSIS locales vers SSIS dans ADF

## <a name="overview"></a>Vue d’ensemble

Quand vous migrez des charges de travail de base de données d’un serveur SQL local vers des services de base de données Azure (Azure SQL Database ou Azure SQL Database Managed Instance), vos charges de travail ETL sur SQL Server Integration Services (SSIS), l’un des principaux services à valeur ajoutée, devront également être migrées.

Azure-SSIS Integration Runtime (IR) dans Azure Data Factory (ADF) prend en charge l’exécution de packages SSIS. Une fois qu’Azure-SSIS IR est provisionné, vous pouvez utiliser des outils familiers comme SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) et des utilitaires de ligne de commande comme dtinstall/dtutil/dtexec pour déployer et exécuter vos packages dans Azure. Pour plus d’informations, consultez [Effectuer un « lift-and-shift » des charges de travail SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Cet article met en évidence le processus de migration de vos charges de travail ETL SSIS locales vers SSIS dans ADF. Le processus de migration se déroule en deux phases : l’**évaluation** et la **migration**.

## <a name="assessment"></a>Évaluation

Pour établir un plan de migration complet, une évaluation minutieuse permettra d’identifier les problèmes liés aux packages SSIS sources, qui pourraient compromettre la migration.

À cette fin, vous pouvez utiliser l’Assistant Migration de données (DMA), un outil disponible en téléchargement gratuit qui peut être installé et exécuté localement. Vous pouvez créer un projet d’évaluation DMA de type **Services d’intégration** pour évaluer des packages SSIS par lots et identifier les problèmes de compatibilité s’inscrivant dans les catégories suivantes :

- Bloqueurs de migration : il s’agit de problèmes de compatibilité qui empêchent l’exécution des packages sources de la migration sur Azure-SSIS IR. DMA fournit des conseils pour vous aider à résoudre ces problèmes.

- Problèmes informationnels : il s’agit de fonctionnalités partiellement prises en charge ou dépréciées utilisées dans les packages sources. DMA fournit un ensemble complet de recommandations, d’approches alternatives disponibles dans Azure et de procédures d’atténuation permettant de résoudre ces problèmes.

### <a name="four-storage-types-for-ssis-packages"></a>Quatre types de stockage pour les packages SSIS

- Catalogue SSIS (SSISDB). Introduit avec SQL Server 2012, il contient un ensemble de procédures stockées, de vues et de fonctions table utilisées pour travailler avec des projets/packages SSIS.
- Système de fichiers.
- Base de données système SQL Server (MSDB).
- Magasin de packages SSIS. Il s’agit d’une couche de gestion des packages couvrant deux sous-types :
  - MSDB : une base de données système dans SQL Server utilisée pour stocker les packages SSIS
  - Système de fichiers géré : un dossier spécifique dans le chemin d’installation de SQL Server, utilisé pour stocker les packages SSIS

DMA prend en charge l’évaluation par lot des packages stockés dans **Système de fichiers**, **Magasin de packages** et **Catalogue SSIS** depuis la **version 5.0 de DMA**.

Obtenez [DMA](https://docs.microsoft.com/sql/dma/dma-overview) et [utilisez-le pour évaluer votre package](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migration

Les étapes de migration des **packages SSIS** et des **travaux de SQL Server Agent** qui planifient les exécutions de packages SSIS peuvent varier selon les [types de stockage](#four-storage-types-for-ssis-packages) des packages SSIS sources et la destination de la migration des charges de travail de base de données. Il existe deux scénarios :

- [**Instance managée Azure SQL Database** en tant que destination des charges de travail de base de données](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** en tant que destination des charges de travail de base de données](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Instance managée Azure SQL Database** en tant que destination des charges de travail de base de données

| **Type de stockage des packages** |Méthode de migration des packages SSIS par lot|Méthode de migration des travaux SSIS par lot|
|-|-|-|
|SSISDB|[Migrez **SSISDB**](scenario-ssis-migration-ssisdb-mi.md).|[Migrez les travaux SSIS vers un agent d’instance managée Azure SQL Database](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Système de fichiers|Redéployez-les sur des partages de fichiers/Azure Files par copie manuelle ou avec dtinstall/dtutil, ou conservez-les dans des systèmes de fichiers pour y accéder par le biais d’un réseau virtuel/d’un IR auto-hébergé. Pour plus d’informations, consultez [dtutil (utilitaire)](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Convertissez-les en activités/déclencheurs/pipelines ADF à l’aide du portail ADF, de SSMS ou de scripts. Pour plus d’informations, consultez l’article sur la [fonctionnalité de planification de SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportez-les vers des systèmes de fichiers/partages de fichiers/Azure Files à l’aide de SSMS/dtutil. Pour plus d’informations, consultez [Importer et exporter des packages (Service SSIS)](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Convertissez-les en activités/déclencheurs/pipelines ADF à l’aide du portail ADF, de SSMS ou de scripts. Pour plus d’informations, consultez l’article sur la [fonctionnalité de planification de SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magasin de packages|Exportez-les vers des systèmes de fichiers/partages de fichiers/Azure Files à l’aide de SSMS/dtutil, redéployez-les sur des partages de fichiers/Azure Files par copie manuelle ou avec dtinstall/dtutil, ou conservez-les dans des systèmes de fichiers pour y accéder par le biais d’un réseau virtuel/d’un IR auto-hébergé. Pour plus d’informations, consultez dtutil (utilitaire). Pour plus d’informations, consultez [dtutil (utilitaire)](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Convertissez-les en activités/déclencheurs/pipelines ADF à l’aide du portail ADF, de SSMS ou de scripts. Pour plus d’informations, consultez l’article sur la [fonctionnalité de planification de SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** en tant que destination des charges de travail de base de données

| **Type de stockage des packages** |Méthode de migration des packages SSIS par lot|Méthode de migration des travaux par lot|
|-|-|-|
|SSISDB|Redéployez-les sur Azure-SSISDB à l’aide de SSDT/SSMS. Pour plus d’informations, consultez [Déployer et exécuter un package SQL Server Integration Services (SSIS) sur Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Convertissez-les en activités/déclencheurs/pipelines ADF à l’aide du portail ADF, de SSMS ou de scripts. Pour plus d’informations, consultez l’article sur la [fonctionnalité de planification de SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Système de fichiers|Redéployez-les sur des partages de fichiers/Azure Files par copie manuelle ou avec dtinstall/dtutil, ou conservez-les dans des systèmes de fichiers pour y accéder par le biais d’un réseau virtuel/d’un IR auto-hébergé. Pour plus d’informations, consultez [dtutil (utilitaire)](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Convertissez-les en activités/déclencheurs/pipelines ADF à l’aide du portail ADF, de SSMS ou de scripts. Pour plus d’informations, consultez l’article sur la [fonctionnalité de planification de SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportez-les vers des systèmes de fichiers/partages de fichiers/Azure Files à l’aide de SSMS/dtutil. Pour plus d’informations, consultez [Importer et exporter des packages (Service SSIS)](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Convertissez-les en activités/déclencheurs/pipelines ADF à l’aide du portail ADF, de SSMS ou de scripts. Pour plus d’informations, consultez l’article sur la [fonctionnalité de planification de SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magasin de packages|Exportez-les vers des systèmes de fichiers/partages de fichiers/Azure Files à l’aide de SSMS/dtutil, redéployez-les sur des partages de fichiers/Azure Files par copie manuelle ou avec dtinstall/dtutil, ou conservez-les dans des systèmes de fichiers pour y accéder par le biais d’un réseau virtuel/d’un IR auto-hébergé. Pour plus d’informations, consultez dtutil (utilitaire). Pour plus d’informations, consultez [dtutil (utilitaire)](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Convertissez-les en activités/déclencheurs/pipelines ADF à l’aide du portail ADF, de SSMS ou de scripts. Pour plus d’informations, consultez l’article sur la [fonctionnalité de planification de SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Ressources supplémentaires

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).
- [Assistant Migration de base de données](https://docs.microsoft.com/sql/dma/dma-overview)
- [Effectuer un « lift-and-shift » des charges de travail SQL Server Integration Services vers le cloud](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Faire migrer des packages SSIS vers une instance managée d’Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Redéployer des packages SQL Server Integration Services vers Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Étapes suivantes

- [Valider les packages SSIS déployés sur Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Exécuter des packages SSIS déployés sur Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Superviser le runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Planifier l’exécution de packages SSIS déployés dans Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
