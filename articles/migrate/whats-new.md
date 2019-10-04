---
title: Nouveautés d’Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble du service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811043"
---
# <a name="whats-new-in-azure-migrate"></a>Nouveautés d’Azure Migrate

[Azure Migrate](migrate-services-overview.md) vous aide à découvrir, à évaluer et à migrer des serveurs, applications et données vers le cloud Microsoft Azure. Cet article récapitule les nouvelles fonctionnalités d’Azure Migrate.



## <a name="azure-migrate-new-version"></a>Nouvelle version d’Azure Migrate

Une nouvelle version d’Azure Migrate a été publiée en juillet 2019. 

- **Version actuelle (nouvelle)**  : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. 
- **Version précédente** : les clients utilisant la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge), doivent désormais utiliser la version actuelle. La version précédente ne permet plus de créer des projets Azure Migrate ou d’effectuer de nouvelles découvertes. Vous pouvez toujours accéder aux projets existants. Pour ce faire, dans le portail Azure, sélectionnez Tous les services, puis recherchez Azure Migrate. Les notifications Azure Migrate contiennent un lien permettant d’accéder aux anciens projets Azure Migrate.


## <a name="azure-migrate-features"></a>Fonctionnalités d’Azure Migrate

La nouvelle version d’Azure Migrate offre un certain nombre de nouvelles fonctionnalités :


- **Plateforme de migration unifiée** : Azure Migrate offre désormais un portail unique pour centraliser, gérer et suivre votre parcours de migration vers Azure, avec une expérience de flux de déploiement et de portail améliorée.
- **Outils d’évaluation et de migration** : Azure Migrate fournit des outils natifs et s’intègre avec d’autres services Azure, ainsi qu’avec des outils d’éditeurs de logiciels indépendants (ISV). [Apprenez-en davantage](migrate-services-overview.md#isv-integration) sur l’intégration d’éditeurs de logiciels indépendants.
- **Évaluation d’Azure Migrate** : l’outil Évaluation de serveur Azure Migrate Server vous permet d’évaluer des machines virtuelles VMware et Hyper-V en vue de leur migration vers Azure. Vous pouvez également évaluer la migration à l’aide d’autres services Azure et outils d’éditeurs de logiciels indépendants.
- **Migration Azure Migrate** : l’outil Migration de serveur Azure Migrate vous permet de migrer vers Azure des machines virtuelles VMware et Hyper-V locales, ainsi que des serveurs physiques, d’autres serveurs virtualisés et des machines virtuelles de cloud privé/public. Vous pouvez également migrer vers Azure à l’aide d’outils d’éditeurs de logiciels indépendants.
- **Appliance Azure Migrate** : Azure Migrate déploie une appliance légère pour la découverte et l’évaluation des machines virtuelles VMware et Hyper-V locales.
    - Les solutions Évaluation de serveur Azure Migrate et Migration de serveur Azure Migrate utilisent cette appliance pour opérer une migration sans agent.
    - L’appliance découvre en permanence les métadonnées et données de performances du serveur à des fins d’évaluation et de migration.  
- **Migration de machine virtuelle VMware** :  La solution Migration de serveur Azure Migrate permet de migrer des machines virtuelles VMware locales vers Azure à l’aide de deux méthodes.  Une migration sans agent à l’aide de l’appliance Azure Migrate, et une migration basée sur un agent, qui utilise une appliance de réplication et déploie un agent sur chaque machine virtuelle que vous souhaitez migrer. [En savoir plus](server-migrate-overview.md)
 - **Évaluation et migration de base de données** : À partir d’Azure Migrate, vous pouvez évaluer des bases de données locales pour la migration vers Azure à l’aide de l’Assistant Migration de base de données Azure. Vous pouvez migrer des bases de données à l’aide d’Azure Database Migration Service.
- **Migration d’application web** : Azure App Service vous permet d’évaluer des applications web à l’aide d’une URL de point de terminaison public. Pour la migration d’applications .NET internes, vous pouvez télécharger et exécuter l’Assistant Migration App Service. 
- **Data Box** : importez de grandes quantités de données hors connexion dans Azure à l’aide d’Azure Data Box dans Azure Migrate.


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
- Essayez nos didacticiels pour évaluer les [machines virtuelles VMware](tutorial-assess-vmware.md) et les [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
