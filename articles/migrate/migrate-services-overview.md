---
title: À propos d’Azure Migrate
description: Découvrez des informations sur l’évaluation et la migration de serveur avec le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a64e7366281f15c94d6551c1f7be27f461737634
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185791"
---
# <a name="about-azure-migrate"></a>À propos d’Azure Migrate

Cet article donne une vue d’ensemble du service Azure Migrate.

Azure Migrate vous aide à migrer votre entreprise d’un environnement local vers Azure. Azure Migrate offre un hub centralisé pour suivre la découverte, l’évaluation et la migration d’une infrastructure, d’applications et de données locales vers Azure.  Azure Migrate fournit :

- **Plateforme de migration unifiée** : Un portail unique pour démarrer, exécuter et suivre votre parcours de migration vers Azure.
- **Panoplie d’outils** : Le hub fournit les outils Azure Migrate pour l’évaluation et la migration, et s’intègre à d’autres services Azure ainsi qu’à d’autres outils et à des offres d’éditeurs de logiciels indépendants.
- **Charges de travail** : Azure Migrate fournit l’évaluation et la migration pour les éléments suivants :
    - **Serveurs** : Utilisez Azure Migrate Server Assessment, Azure Migrate Server Migration et d’autres outils pour l’évaluation et la migration de serveurs vers des machines virtuelles Azure.
    - **Bases de données** : Tirez parti des outils de Microsoft et d’éditeurs de logiciels indépendants pour l’évaluation et la migration de bases de données locales vers Azure SQL DB ou Azure SQL Managed Instance.
    - **Applications web** : utilisez l’Assistant Azure App Service pour évaluer et migrer des applications web locales vers Azure App Service.
    - **Bureaux virtuels** : utilisez les outils d’éditeurs de logiciels indépendants pour évaluer et migrer une infrastructure VDI (Virtual Desktop Infrastructure) locale vers Windows Virtual Desktop dans Azure.
    - **Données** : Utilisez la famille de produits Azure Data Box pour migrer rapidement et à moindre coût de grandes quantités de données vers Azure.

## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe actuellement deux versions du service Azure Migrate :

- **Version actuelle** : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. [Apprenez-en davantage](whats-new.md) sur les nouveautés de cette version.
- **Version précédente** : Si vous utilisiez la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge), vous devez désormais utiliser la version actuelle. Vous ne pouvez plus créer de projets Azure Migrate à l’aide de la version précédente, et nous vous recommandons de ne pas effectuer de nouvelles découvertes. Pour accéder à des projets existants, dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**. Le tableau de bord Azure Migrate affiche une notification et un lien permettant d’accéder aux anciens projets Azure Migrate.



## <a name="isv-integration"></a>Intégration d’éditeurs de logiciels indépendants

En plus des outils Azure natifs, Azure Migrate s’intègre avec un certain nombre d’offres d’éditeurs de logiciels indépendants. 

**Fournisseurs de logiciels indépendants** | **Fonctionnalité**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrer des serveurs
[Cloudamize](https://www.cloudamize.com/platform) | Évaluer des serveurs
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Évaluer et migrer des serveurs
[Device 42](https://docs.device42.com/) | Évaluer des serveurs
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Évaluer l’infrastructure VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrer des serveurs
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Évaluer des serveurs
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Évaluer des serveurs et des bases de données

## <a name="azure-tool-integration"></a>Intégration d’outils Azure

Le tableau récapitule les autres outils intégrés à Azure Migrate.

**Outil** | **Détails**
--- | ---
Azure Migrate : Server Assessment | Évaluer des serveurs
Azure Migrate : Server Migration | Migrer des serveurs
Assistant Migration de données Microsoft (DMA) | Évaluer des bases de données
Database Migration Service (DMS) | Migrer des bases de données
Movere | Évaluer des serveurs
Assistant Migration d’applications web | Évaluer et migrer des applications web



### <a name="selecting-a-tool"></a>Sélection d’un outil

Identifiez l’outil dont vous avez besoin, puis ajoutez-le à un projet Azure Migrate.

- Si vous ajoutez un outil d’un éditeur de logiciels indépendant ou Movere :
    - Commencez par obtenir une licence ou par vous inscrire à un essai gratuit, conformément à la stratégie de l’outil. La gestion des licences pour les outils se fait conformément au modèle de licence de l’éditeur de logiciels indépendant ou de l’outil.
    - Dans chaque outil, il existe une option permettant de se connecter à Azure Migrate. Suivez les instructions et la documentation de l’outil pour connecter celui-ci avec Azure Migrate.
- Vous pouvez suivre de façon centralisée votre parcours de migration depuis le projet Azure Migrate, dans Azure et d’autres outils.



## <a name="azure-migrate-server-assessment-tool"></a>Outil d’évaluation de serveur Azure Migrate

Azure Migrate : L’outil Évaluation de serveur découvre et évalue des machines virtuelles VMware et Hyper-V ainsi que des serveurs physiques locaux en vue de leur migration vers Azure. Il vous aide à identifier les aspects suivants :

- **État de préparation pour Azure :** évaluez si les machines locales sont prêtes pour la migration vers Azure.
- **Dimensionnement d’Azure :** taille estimée des machines virtuelles Azure après la migration.
- **Estimation des coûts Azure :** coûts estimés liés à l’exécution de serveurs locaux dans Azure.
- **Visualisation des dépendances :** Dépendances entre serveurs (si la visualisation des dépendances est activée) et méthodes optimales pour déplacer des serveurs dépendants vers Azure.

L’outil Évaluation de serveur utilise une appliance légère que vous déployez localement et inscrivez avec l’outil.

- L’appliance découvre des machines locales.
- Elle se connecte à l’outil Évaluation de serveur et envoie en continu des métadonnées et des données de performances à Azure Migrate.
- La découverte de l’appliance se fait sans agent. Rien ne doit explicitement être installé sur les machines découvertes.
- Après la découverte, vous rassemblez les machines découvertes dans des groupes. En général, les machines qui seront migrées ensemble sont rassemblées dans un même groupe.
- Créez une évaluation pour un groupe. Vous analysez ensuite l’évaluation pour déterminer votre stratégie de migration.

## <a name="azure-migrate-server-migration-tool"></a>Outil Migration de serveur Azure Migrate

Azure Migrate : L’outil Migration de serveur vous aide à migrer des machines virtuelles VMware et Hyper-V ainsi que des serveurs physiques locaux, d’autres machines virtualisées et des machines virtuelles de cloud public vers Azure. Vous pouvez migrer des machines après les avoir évaluées ou sans les avoir évaluées.


## <a name="database-migration-assistant"></a>Assistant Migration de base de données

Azure Migrate s’intègre avec l’Assistant Migration de données (DMA) de Microsoft pour évaluer les bases de données SQL Server locales en vue de leur migration vers Azure SQL DB, Azure SQL Managed Instance ou des machines virtuelles Azure exécutant SQL Server. DMA fournit des informations sur les problèmes de blocage potentiels pour la migration. Il identifie les fonctionnalités non prises en charge, ainsi que de nouvelles fonctionnalités dont vous pourrez tirer bénéficier après la migration, et vous aide à identifier le chemin d’accès approprié pour la migration de base de données. [Plus d’informations](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)

## <a name="database-migration-service"></a>Database Migration Service

Azure Migrate s’intègre avec Azure Database Migration Service (DMS) pour migrer des bases de données locales vers Azure. Utilisez DMS pour migrer des bases de données locales vers des machines virtuelles Azure exécutant SQL, Azure SQL DB et Azure SQL Managed instances. [Plus d’informations](https://docs.microsoft.com/azure/dms/dms-overview)

## <a name="movere"></a>Movere

 
Movere est une plateforme SaaS qui améliore les fonctionnalités décisionnelles en présentant avec précision des environnements informatiques complets en une seule journée. À mesure que les organisations se développent, évoluent et optimisent leur numérisation, la solution offre aux entreprises la confiance dont elles ont besoin pour avoir une visibilité et un contrôle de leurs environnements, quelle que soit la plateforme, l’application ou l’emplacement géographique. Movere a été [acheté](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) par Microsoft et n’est plus vendu en tant qu’offre autonome.  Movere est disponible via les programmes « Microsoft Solution Assessment » et « Cloud Economics ». [En savoir plus](https://www.movere.io) sur Movere. Si vous avez des questions, envoyez-les à l’adresse movereq@microsoft.com ou contactez votre représentant Microsoft.

Nous vous encourageons à examiner aussi Azure Migrate, notre service de migration intégré. Azure Migrate fournit un hub central pour simplifier votre migration vers le cloud. Le hub offre une prise en charge complète de différentes charges de travail, notamment des serveurs physiques et virtuels, des bases de données et des applications. La visibilité de bout en bout facilite le suivi de la progression tout au long de la découverte, de l’évaluation et de la migration. Avec les outils intégrés d’Azure et des éditeurs de logiciels indépendants partenaires, Azure Migrate a aussi un large éventail de fonctionnalités, notamment la découverte des serveurs virtuels et physiques, le dimensionnement adapté en fonction des performances, la planification des coûts, les évaluations basées sur l’importation et l’analyse des dépendances des applications sans agent. Si vous recherchez des conseils d’experts pour commencer, Microsoft a des partenariats avec des [fournisseurs de services managés spécialisés Azure](https://azure.microsoft.com/partners) compétents pour vous guider dans cette démarche. Consultez le [site web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Assistant Migration d’applications web

Azure Migrate s’intègre à l’Assistant Migration Azure App Service. À partir du hub Azure Migrate, vous pouvez évaluer et migrer des applications web locales vers Azure, à l’aide de l’Assistant, de la manière suivante :

- **Évaluer les applications en ligne** : utilisez l’Assistant Migration Azure App Service pour évaluer les sites web locaux en vue de leur migration vers Azure App Service.
- **Migrer des applications web** : migrez des applications web .NET et PHP vers Azure à l’aide de l’Assistant Migration Azure App Service.

[En savoir plus](https://appmigration.microsoft.com/) sur l’Assistant.



## <a name="offline-data-migration"></a>Migration de données hors connexion

Pour déplacer de grandes quantités de données hors connexion vers Azure, vous pouvez utiliser la gamme de produits Azure Data Box. [En savoir plus](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Étapes suivantes

- Essayez nos didacticiels pour évaluer les [machines virtuelles VMware](tutorial-assess-vmware.md) et les [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
- [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
