---
title: Série de migration Contoso | Microsoft Docs
description: Fournit une vue d’ensemble de la stratégie et des scénarios de migration utilisés par Contoso pour migrer son centre de données local vers Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939183"
---
# <a name="contoso-migration-series"></a>Série de migration Contoso


Nous disposons d’une série d’articles montrant comment l’organisation fictive Contoso migre une infrastructure locale vers le cloud [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/). 

La série comprend des informations et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios augmentent en complexité à mesure qu’ils évoluent. Les articles expliquent comment la société Contoso effectue sa mission de migration, mais des liens vers des lectures générales et des instructions spécifiques y sont fournis.

## <a name="migration-articles"></a>Articles sur la migration

Les articles de la série sont résumés dans le tableau ci-dessous.  

- Chaque scénario de migration est piloté par des objectifs métier légèrement différents, qui déterminent la stratégie de migration.
- Pour chaque scénario de déploiement, nous fournissons des informations sur les axes stratégiques et les objectifs, une proposition d’architecture, des étapes pour effectuer la migration,ainsi que des recommandations pour le nettoyage et des étapes à effectuer une fois la migration terminée.

**Article** | **Détails** 
--- | --- 
[Article 1 : Vue d’ensemble](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Vue d’ensemble de la série d’articles, de la stratégie de migration de Contoso et des exemples d’applications utilisés dans la série. 
[Article 2 : Déployer une infrastructure Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration de la série. 
[Article 3 : Évaluer les ressources locales à migrer vers Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso évalue son application locale SmartHotel360 qui s’exécute sur VMware. Contoso évalue les machines virtuelles de l’application à l’aide du service Azure Migrate et la base de données SQL Server de l’application à l’aide de l’Assistant Migration de données.
[Article 4 : Réhéberger une application sur une machine virtuelle Azure et SQL Database Managed Instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso exécute une migration lift-and-shift vers Azure pour son application SmartHotel360 locale. Contoso migre la machine virtuelle front-end de l’application à l’aide [d’Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migre la base de données de l’application vers Azure SQL Database Managed Instance à l’aide [d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migre les machines virtuelles de son application SmartHotel360 vers des machines virtuelles Azure avec le service Site Recovery. 
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et dans un groupe de disponibilité SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migre l’application SmartHotel360. Contoso utilise Site Recovery pour migrer les machines virtuelles d’application. La société utilise Database Migration Service pour migrer la base de données d’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. 
[Article 7 : Réhéberger une application Linux sur des machines virtuelles Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso effectue une migration lift-and-shift de son application osTicket Linux sur des machines virtuelles Azure à l’aide du service Site Recovery.
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et Azure Database pour MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migre son application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery. La société migre la base de données d’application vers Azure Database pour MySQL à l’aide de MySQL Workbench. 
[Article 9 : Refactoriser une application vers une application web Azure et Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migre son application SmartHotel360 vers une application web Azure, et la base de données de l’application vers une instance Azure SQL Server avec l’Assistant Migration de données.     
[Article 10 : Refactoriser une application Linux vers une application web Azure et Azure Database pour MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migre son application osTicket Linux vers Azure Web App dans plusieurs régions Azure à l’aide d’Azure Traffic Manager, intégré à GitHub de façon à assurer une livraison continue. Contoso migre la base de données d’application vers une instance d’Azure Database pour MySQL. 
[Article 11 : Refactoriser Team Foundation Server sur Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migre son déploiement local de Team Foundation Server vers Azure DevOps Services dans Azure.
[Article 12 : Réarchitecturer une application dans des conteneurs Azure et Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migre son application SmartHotel vers Azure. Ensuite, l’entreprise restructure la couche web de l’application comme un conteneur Windows s’exécutant dans Azure Service Fabric, et la base de données avec Azure SQL Database.
[Article 13 : Regénérer une application dans Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso régénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services et Azure Cosmos DB.
[Article 14 : Mettre à l’échelle une migration vers Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Après des essais de différentes combinaisons de migration, Contoso se prépare à une migration complète vers Azure.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) la migration cloud.
- Découvrez les stratégies des migrations pour les autres scénarios (paires source/cible) dans le [guide de migration de base de données](https://datamigration.microsoft.com/).
