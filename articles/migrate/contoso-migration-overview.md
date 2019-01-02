---
title: Vue d’ensemble de la migration de Contoso vers Azure | Microsoft Docs
description: Fournit une vue d’ensemble de la stratégie et des scénarios de migration utilisés par Contoso pour migrer son centre de données local vers Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: f18af40c67195ee2e1e74c04c0834eea06fb14d1
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191952"
---
# <a name="contoso-migration-overview"></a>Migration de Contoso : Vue d’ensemble


Cet article montre comment l’organisation fictive Contoso migre une infrastructure locale vers le cloud [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/). 

Ce document est le premier d’une série d’articles qui montrent comment la société fictive Contoso migre vers Azure. La série comprend des informations et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios croissant en complexité, nous ajouterons des articles au fil du temps. Les articles expliquent comment la société Contoso effectue sa mission de migration, mais des liens vers des lectures générales et des instructions spécifiques y sont fournis.

## <a name="introduction"></a>Introduction

Azure fournit l’accès à un ensemble complet de services cloud. En tant que développeur et professionnel de l’informatique, vous pouvez utiliser ces services pour créer, déployer et gérer des applications sur un éventail d’outils et de frameworks à travers un réseau mondial de centres de données. Votre entreprise étant confrontée aux défis associés à la transition numérique, le cloud Azure vous permet de trouver la solution pour optimiser les ressources et les opérations, communiquer avec vos clients et employés et transformer vos produits.

Toutefois, Azure reconnaît que, malgré tous les avantages proposés par le cloud en termes de vitesse et de flexibilité, de réduction des coûts, de performances et de fiabilité, de nombreuses organisations vont devoir exécuter des centres de données locaux pendant un certain temps. C’est pour répondre à ces barrières concernant l’adoption du cloud qu’Azure propose une stratégie de cloud hybride qui crée des ponts entre vos centres de données locaux et le cloud public Azure. Par exemple, vous pouvez utiliser des ressources cloud Azure comme Sauvegarde Azure pour protéger des ressources locales, ou utiliser des analyses Azure pour obtenir des informations sur les charges de travail locales. 

Dans le cadre de la stratégie de cloud hybride, Azure fournit toujours plus de solutions pour migrer les applications et les charges de travail locales vers le cloud. En suivant une procédure simple, vous pouvez évaluer l’intégralité de vos ressources locales pour déterminer comment elles s’exécuteront dans le cloud Azure. Une fois cette évaluation approfondie obtenue, vous pouvez migrer des ressources vers Azure en toute confiance. Quand les ressources sont opérationnelles dans Azure, vous pouvez les optimiser pour conserver et améliorer l’accès, la flexibilité, la sécurité et la fiabilité.

## <a name="migration-strategies"></a>Stratégies de migration

Les stratégies pour la migration vers le cloud sont classées en quatre grandes catégories : réhébergement, refactorisation, réarchitecture ou recréation. La stratégie que vous adoptez dépend de facteurs qui pilotent votre activité et des objectifs de la migration. Vous pouvez adopter plusieurs stratégies. Par exemple, vous pouvez choisir de réhéberger des applications simples (lift-and-shift) ou des applications qui ne sont pas critiques pour votre activité, mais réarchitecturer celles qui sont plus complexes et critiques pour votre activité. Examinons les stratégies.


**Stratégie** | **Définition** | **Quand utiliser** 
--- | --- | --- 
**Réhébergement** | Souvent appelé migration « lift-and-shift ». Cette option ne nécessite pas de modifications du code et elle vous permet de migrer rapidement vos applications existantes vers Azure. Chaque application est migrée en l’état, pour profiter des avantages du cloud, sans les risques et les coûts associés aux modifications du code. | Quand vous devez déplacer rapidement des applications dans le cloud.<br/><br/> Quand vous voulez déplacer une application sans la modifier.<br/><br/> Quand vos applications sont architecturées de façon à tirer parti de la scalabilité [de l’IaaS Azure](https://azure.microsoft.com/overview/what-is-iaas/) après la migration.<br/><br/> Quand des applications sont importantes pour votre activité, mais que vous n’avez pas besoin de modifications immédiates de leurs fonctionnalités.
**Refactorisation** | Souvent appelée « repackaging », la refactorisation nécessite des modifications minimales des applications pour qu’elles puissent se connecter au [PaaS Azure](https://azure.microsoft.com/overview/what-is-paas/) et utiliser des offres cloud.<br/><br/> Par exemple, vous pouvez migrer vos applications existantes vers Azure App Service ou Azure Kubernetes Service.<br/><br/> Vous pouvez aussi refactoriser vos bases de données relationnelles et non relationnelles dans Azure SQL Database Managed Instance, Azure Database pour MySQL, Azure Database pour PostgreSQL ou Azure Cosmos DB. | Si votre application peut être facilement repackagée pour fonctionner dans Azure.<br/><br/> Si vous voulez appliquer des pratiques DevOps innovantes fournie par Azure, ou si vous envisagez d’utiliser DevOps avec une stratégie de conteneur pour les charges de travail.<br/><br/> Pour la refactorisation, vous devez penser à la portabilité de votre base de code existante et à la disponibilité des compétences en développement.
**Réarchitecture** | La réarchitecture pour la migration porte principalement sur la modification et l’extension des fonctionnalités et de la base de code de l’application, avec comme objectif d’optimiser l’architecture de l’application pour la scalabilité du cloud.<br/><br/> Par exemple, vous pouvez décomposer une application monolithique en un groupe de microservices qui fonctionnent ensemble et sont facilement mis à l’échelle.<br/><br/> Vous pouvez aussi réarchitecturer vos bases de données relationnelles et non relationnelles pour des solutions DBaaS entièrement managées, comme Azure SQL Database Managed Instance, Azure Database pour MySQL, Azure Database pour PostgreSQL et Azure Cosmos DB. | Quand votre application nécessite des révisions majeures pour incorporer de nouvelles fonctionnalités, ou pour fonctionner efficacement sur une plateforme cloud.<br/><br/> Quand vous voulez utiliser des investissements faits dans des applications existantes, répondre à des exigences d’extensibilité, appliquer des pratiques Azure DevOps innovantes et minimiser l’utilisation de machines virtuelles.
**Recréation** | La recréation va encore plus loin en recréant une application à partir de rien, en utilisant les technologies du cloud Azure.<br/><br/> Par exemple, vous pouvez créer des applications entièrement nouvelles avec des technologies cloud natives comme Azure Functions, Azure AI, Azure SQL Database Managed Instance et Azure Cosmos DB. | Quand vous voulez un développement rapide, et que les applications existantes ont des fonctionnalités et une durée de vie limitées.<br/><br/> Quand vous êtes prêt à accélérer l’innovation pour votre activité (notamment les pratiques DevOps fournies par Azure), à créer de nouvelles applications en utilisant les technologies cloud natives et à tirer parti des avancées de l’IA, de Blockchain et d’IoT.

## <a name="migration-articles"></a>Articles sur la migration

Les articles de la série sont résumés dans le tableau ci-dessous.  

- Chaque scénario de migration est piloté par des objectifs métier légèrement différents, qui déterminent la stratégie de migration.
- Pour chaque scénario de déploiement, nous fournissons des informations sur les axes stratégiques et les objectifs, une proposition d’architecture, des étapes pour effectuer la migration,ainsi que des recommandations pour le nettoyage et des étapes à effectuer une fois la migration terminée.

**Article** | **Détails** | **État**
--- | --- | ---
Article 1 : Vue d’ensemble | Vue d’ensemble de la série d’articles, de la stratégie de migration de Contoso et des exemples d’applications utilisés dans la série. | Cet article
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration de la série. | Disponible
[Article 3 : Évaluer les ressources locales à migrer vers Azure](contoso-migration-assessment.md)  | Contoso évalue son application locale SmartHotel360 qui s’exécute sur VMware. Contoso évalue les machines virtuelles de l’application à l’aide du service Azure Migrate et la base de données SQL Server de l’application à l’aide de l’Assistant Migration de données. | Disponible
[Article 4 : Réhéberger une application sur une machine virtuelle Azure et SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso exécute une migration lift-and-shift vers Azure pour son application SmartHotel360 locale. Contoso migre la machine virtuelle front-end de l’application à l’aide [d’Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migre la base de données de l’application vers Azure SQL Database Managed Instance à l’aide [d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible   
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Contoso migre les machines virtuelles de son application SmartHotel360 vers des machines virtuelles Azure avec le service Site Recovery. | Disponible   [Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Contoso migre les machines virtuelles de son application SmartHotel360 vers des machines virtuelles Azure avec le service Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et dans un groupe de disponibilité SQL Server ](contoso-migration-rehost-vm-sql-ag.md) | Contoso migre l’application SmartHotel360. Contoso utilise Site Recovery pour migrer les machines virtuelles d’application. La société utilise Database Migration Service pour migrer la base de données d’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible [Article 7 : Réhéberger une application Linux sur des machines virtuelles](contoso-migration-rehost-linux-vm.md) | Contoso effectue une migration lift-and-shift de son application osTicket Linux sur des machines virtuelles Azure à l’aide du service Site Recovery. | Disponible
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et Azure Database pour MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migre son application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery. La société migre la base de données d’application vers Azure Database pour MySQL à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application vers une application web Azure et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migre son application SmartHotel360 vers une application web Azure, et la base de données de l’application vers une instance Azure SQL Server avec l’Assistant Migration de données. | Disponible    
[Article 10 : Refactoriser une application Linux vers une application web Azure et Azure Database pour MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migre son application osTicket Linux vers Azure Web App dans plusieurs régions Azure à l’aide d’Azure Traffic Manager, intégré à GitHub de façon à assurer une livraison continue. Contoso migre la base de données d’application vers une instance d’Azure Database pour MySQL. | Disponible
[Article 11 : Refactoriser Team Foundation Server sur Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso migre son déploiement local de Team Foundation Server vers Azure DevOps Services dans Azure. | Disponible
[Article 12 : Réarchitecturer une application dans des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migre son application SmartHotel vers Azure. Ensuite, l’entreprise restructure la couche web de l’application comme un conteneur Windows s’exécutant dans Azure Service Fabric, et la base de données avec Azure SQL Database. | Disponible    
[Article 13 : Regénérer une application dans Azure](contoso-migration-rebuild.md) | Contoso régénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services et Azure Cosmos DB. | Disponible 
[Article 14 : Mettre à l’échelle une migration vers Azure](contoso-migration-scale.md) | Après des essais de différentes combinaisons de migration, Contoso se prépare à une migration complète vers Azure. | Disponible

Dans cet article, Contoso configure tous les éléments d’infrastructure nécessaires à tous les scénarios de migration. 







### <a name="demo-apps"></a>Applications de démonstration

Les articles utilisent deux applications de démonstration : SmartHotel360 et osTicket.

- **SmartHotel360** : cette application a été développée par Microsoft comme application de test que vous pouvez utiliser quand vous travaillez avec Azure. Elle est fournie en open source et vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360). Il s’agit d’une application ASP.NET connectée à une base de données SQL Server. Actuellement, l’application fonctionne sur deux machines virtuelles VMware exécutant Windows Server 2008 R2 et SQL Server 2008 R2. Les machines virtuelles de l’application sont hébergées localement et gérées par vCenter Server.
- **osTicket** : application open source de tickets pour Service Desk qui s’exécute sur Linux. Vous pouvez le télécharger à partir de [GitHub](https://github.com/osTicket/osTicket). Actuellement, l’application fonctionne sur deux machines virtuelles VMware exécutant Ubuntu 16.04 LTS, avec Apache 2, PHP 7.0 et MySQL 5.7.
    

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment](contoso-migration-infrastructure.md) Contoso configure une infrastructure locale et Azure pour préparer la migration.



