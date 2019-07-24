---
title: À propos d’Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble du service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227806"
---
# <a name="about-azure-migrate"></a>À propos d’Azure Migrate

Cet article donne une vue d’ensemble du service Azure Migrate.

Azure Migrate vous aide à migrer vers Azure. Azure Migrate offre un hub centralisé pour suivre la découverte, l’évaluation et la migration d’une infrastructure, d’applications et de données locales vers Azure. Le hub fournit des outils Azure pour l’évaluation et la migration, ainsi que des offres d’éditeurs de logiciels indépendant (ISV) tiers. Il fournit :

- **Plateforme de migration unifiée** : utilisez un portail unique pour démarrer, exécuter et suivre votre parcours de votre migration vers Azure.
- **Panoplie d’outils** : Azure Migrate fournit des outils natifs et s’intègre avec d’autres services Azure, ainsi qu’avec des outils d’éditeurs de logiciels indépendants. Sélectionnez les outils d’évaluation et de migration appropriée, en fonction des exigences de votre organisation. 
- **Évaluation de serveur Azure Migrate** : utilisez l’outil Évaluation de serveur pour évaluer des machines virtuelles VMware et Hyper-V locales en vue de leur migration vers Azure.
- **Migration de serveur Azure Migrate** : utilisez l’outil Migration de serveur pour migrer des machines virtuelles VMware et Hyper-V locales, des machines virtuelles cloud et des serveurs physiques locaux vers Azure.
- **Évaluation de base de données Azure Migrate** : évaluez des bases de données locales en vue de leur migration vers Azure.
- **Migration de base de données Azure Migrate** : migrez des bases de données locales vers Azure.


## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe deux versions du service Azure Migrate :

- **Version actuelle** : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. [Apprenez-en davantage](whats-new.md) sur les nouveautés de cette version.
- **Version précédente** : Si vous utilisiez la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge), vous devez désormais utiliser la version actuelle. Vous ne pouvez plus créer de projets Azure Migrate à l’aide de la version précédente, et nous vous recommandons de ne pas effectuer de nouvelles découvertes. Pour accéder à des projets existants, dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**. Le tableau de bord Azure Migrate affiche une notification et un lien permettant d’accéder aux anciens projets Azure Migrate.

## <a name="isv-integration"></a>Intégration d’éditeurs de logiciels indépendants

En plus des outils Azure natifs, Azure Migrate s’intègre avec un certain nombre d’offres d’éditeurs de logiciels indépendants. Vous identifiez l’outil dont vous avez besoin, puis l’ajoutez à un projet Azure Migrate. Vous pouvez suivre de manière centralisée votre parcours de migration depuis le projet Azure Migrate, sur l’ensemble des outils Azure et d’éditeurs de logiciels indépendants.

**Fournisseurs de logiciels indépendants** | **Fonctionnalité**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Évaluation
[Device 42](https://docs.device42.com/) | Évaluation
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Évaluation
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Évaluation
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Évaluer et migrer
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrer

### <a name="selecting-an-isv-tool"></a>Sélection d’un outil d’éditeur de logiciels indépendant

Après avoir ajouté un outil d’éditeur de logiciels indépendant à un projet Azure Migrate, commencez à vous servir de l’outil pour obtenir une licence ou vous inscrire pour un essai gratuit, conformément à la stratégie de l’éditeur de logiciels indépendant. Dans chaque outil, il existe une option permettant de se connecter à Azure Migrate. Suivez les instructions et la documentation de l’outil pour connecter celui-ci avec Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Évaluation de serveur Azure Migrate

L’outil Évaluation de serveur Azure Migrate découvre et évalue des machines virtuelles VMware et Hyper-V locales en vue de leur migration vers Azure. Il vous aide à identifier les aspects suivants :

- **État de préparation pour Azure :** évaluez si les machines locales sont prêtes pour la migration vers Azure.
- **Dimensionnement d’Azure :** estimez la taille des machines virtuelles Azure après la migration.
- **Estimation des coûts Azure :** estimez les coûts liés à l’exécution de serveurs locaux dans Azure.
- **Visualisation des dépendances :** identifiez les dépendances entre serveurs et la meilleure façon de déplacer des serveurs dépendants vers Azure. 

L’outil Évaluation de serveur utilise une appliance légère que vous déployez localement et inscrivez avec l’outil.

- L’appliance découvre des machines locales, se connecte à l’outil Évaluation de serveur et envoie en continu des métadonnées et des données relatives aux performances à Azure Migrate.
- La découverte se fait sans agent. Rien ne doit explicitement être installé sur les machines virtuelles découvertes.
- Une fois les machines découvertes, vous les rassemblez dans des groupes qui se composent généralement de machines virtuelles que vous souhaitez migrer ensemble.
- Créez une évaluation pour un groupe. Vous pouvez ensuite analyser l’évaluation pour déterminer votre stratégie de migration.

## <a name="azure-migrate-server-migration"></a>Migration de serveur Azure Migrate

L’outil Migration de serveur Azure Migrate vous aide à migrer des machines virtuelles VMware et Hyper-V ainsi que des serveurs physiques locaux, d’autres machines virtualisées et des machines virtuelles de cloud public vers Azure. Vous pouvez migrer des machines après les avoir évaluées ou sans les avoir évaluées. 

## <a name="azure-migrate-database-assessment"></a>Évaluation de base de données Azure Migrate

Azure Migrate s’intègre avec l’Assistant Migration de données (DMA) pour évaluer les bases de données SQL Server locales pour la migration vers Azure SQL DB, Azure SQL Managed Instance ou des machines virtuelles Azure exécutant SQL Server. DMA fournit des informations sur les problèmes de blocage potentiels pour la migration. Il identifie les fonctionnalités non prises en charge, ainsi que de nouvelles fonctionnalités dont vous pourrez tirer bénéficier après la migration, et vous aide à identifier le chemin d’accès approprié pour la migration de base de données. [Plus d’informations](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)


## <a name="azure-migrate-database-migration"></a>Migration de base de données Azure Migrate

Azure Migrate s’intègre avec Azure Database Migration Service (DMS) pour migrer des bases de données locales vers Azure. Utilisez DMS pour migrer des bases de données locales vers des machines virtuelles Azure exécutant SQL, Azure SQL DB et Azure SQL Managed instances. [Plus d’informations](https://docs.microsoft.com/azure/dms/dms-overview)

## <a name="web-app-assessment-and-migration"></a>Évaluation et migration d’application web

À partir du hub Azure Migrate, vous pouvez évaluer et migrer des applications web locales vers Azure.

- **Évaluer les applications en ligne** : utilisez l’Assistant Migration Azure App Service pour évaluer les sites web locaux en vue de leur migration vers Azure App Service.
- **Migrer des applications web** : migrez des applications web .NET et PHP vers Azure à l’aide de l’Assistant Migration Azure App Service.

[En savoir plus.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migration de données hors connexion

Pour déplacer de grandes quantités de données vers Azure, vous pouvez utiliser la famille de produits hors connexion Data Box. [En savoir plus](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Étapes suivantes

- Essayez nos didacticiels pour évaluer les [machines virtuelles VMware](tutorial-assess-vmware.md) et les [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
- [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
