---
title: À propos d’Azure Migrate
description: Découvrez le service Azure Migrate.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127748"
---
# <a name="about-azure-migrate"></a>À propos d’Azure Migrate

Cet article donne une vue d’ensemble du service Azure Migrate.

Azure Migrate fournit un hub centralisé pour évaluer et migrer les serveurs, l’infrastructure, les applications et les données en local vers Azure. Azure Migrate fournit les fonctionnalités suivantes :

- **Plateforme de migration unifiée** : Un portail unique pour démarrer, exécuter et suivre votre parcours de migration vers Azure.
- **Panoplie d’outils** : Panoplie d’outils d’évaluation et de migration. Les outils incluent Azure Migrate : évaluation de serveur et Azure Migrate : Server Migration. Azure Migrate s’intègre avec d’autres services Azure ainsi qu’avec d’autres outils et offres d’ISV (éditeur de logiciels indépendant).
- **Évaluation et migration** : dans le hub Azure Migrate, vous pouvez évaluer et migrer les éléments suivants :
    - **Serveurs** : évaluez et migrez des serveurs locaux vers des machines virtuelles Azure.
    - **Bases de données** : évaluez et migrez des bases de données locales vers Azure SQL DB ou vers Azure SQL Managed Instance.
    - **Applications web** : évaluez et migrez des applications web locales vers Azure App Service à l’aide de l’Assistant Azure App Service.
    - **Bureaux virtuels** : évaluez et migrez votre infrastructure VDI (infrastructure de bureau virtuel) locale vers Windows Virtual Desktop dans Azure.
    - **Données** : migrez rapidement et à moindre coût de grandes quantités de données vers Azure à l’aide des produits Azure Data Box. 


## <a name="integrated-tools"></a>Outils intégrés

Le hub Azure Migrate comprend ces outils.

**Outil** | **Évaluer/migrer** | **Détails**
--- | --- | ---
**Azure Migrate : évaluation de serveur** | Évaluez des serveurs. | Découvrez et évaluez les machines virtuelles VMware, les machines virtuelles Hyper-V et les serveurs physiques locaux pour les préparer à une migration vers Azure.
**Azure Migrate : migration de serveur** | Migrez des serveurs. | Migrez des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques, d’autres machines virtualisées ainsi que des machines virtuelles de cloud public vers Azure. 
**Assistant Migration de données Microsoft (DMA)** | Évaluez des bases de données SQL Server locales pour les migrer vers Azure SQL DB, vers Azure SQL Managed Instance ou vers des machines virtuelles Azure exécutant SQL Server. | DMA permet d’identifier les problèmes de blocage potentiels pour la migration. Il identifie les fonctionnalités non prises en charge et les nouvelles fonctionnalités dont vous pouvez bénéficier après la migration. De plus, il vous aide à déterminer le chemin approprié pour la migration de base de données. [Plus d’informations](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)
**Database Migration Service (DMS)** | Migrez des bases de données locales vers des machines virtuelles Azure exécutant SQL, Azure SQL DB et Azure SQL Managed instances. | [Découvrez-en plus](https://docs.microsoft.com/azure/dms/dms-overview) sur DMS.
**Movere** | Évaluez des serveurs. | [En savoir plus](#movere) sur Movere.
**Assistant Migration d’applications web** | Évaluez et migrez des applications web locales vers Azure. |  Utilisez l’outil Migration Assistant d’Azure App Service pour évaluer les sites web locaux à migrer vers Azure App Service.<br/><br/> Utilisez l’outil Assistant pour migrer des applications web .NET et PHP vers Azure. [Découvrez plus en détail](https://appmigration.microsoft.com/) l’outil Migration Assistant d’Azure App Service.
**Azure Data Box** | Migration de données hors connexion. | Pour déplacer de grandes quantités de données hors connexion vers Azure, utilisez la gamme de produits Azure Data Box. [Plus d’informations](https://docs.microsoft.com/azure/databox/)

## <a name="isv-integration"></a>Intégration d’éditeurs de logiciels indépendants

Azure Migrate s’intègre à un certain nombre d’offres d’éditeurs de logiciels indépendants. 

**Fournisseurs de logiciels indépendants**    | **Fonctionnalité**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrer des serveurs
[Cloudamize](https://www.cloudamize.com/platform) | Évaluer des serveurs
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Évaluer et migrer des serveurs
[Device 42](https://docs.device42.com/) | Évaluer des serveurs
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Évaluer l’infrastructure VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrer des serveurs
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Évaluer des serveurs
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Évaluer des serveurs et des bases de données


## <a name="azure-migrate-server-assessment-tool"></a>Outil d’évaluation de serveur Azure Migrate

L’outil Azure Migrate : évaluation de serveur permet de découvrir et d’évaluer les machines virtuelles VMware, les machines virtuelles Hyper-V et les serveurs physiques locaux pour une migration vers Azure. Voici ce que fait l’outil :

- **État de préparation pour Azure :** vous permet d’évaluer si les machines locales sont prêtes pour une migration vers Azure.
- **Dimensionnement d’Azure :** vous permet d’estimer la taille des machines virtuelles Azure après la migration.
- **Estimation des coûts Azure :** coûts estimés liés à l’exécution de serveurs locaux dans Azure.
- **Analyse des dépendances :** si vous utilisez l’outil d’évaluation de serveur avec l’[analyse des dépendances](concepts-dependency-visualization.md), vous pouvez identifier efficacement les dépendances entre serveurs et optimiser les stratégies de déplacement des serveurs interdépendants vers Azure.


L’outil d’évaluation de serveur utilise une [appliance Azure Migrate](migrate-appliance.md) légère que vous déployez localement.

- L’appliance s’exécute sur une machine virtuelle ou un serveur physique. Vous pouvez l’installer facilement à l’aide d’un modèle téléchargé.
- Elle détecte les machines locales et envoie en continu des métadonnées et des données de performances sur les machines à Azure Migrate.
- La découverte de l’appliance se fait sans agent. Rien n’est installé sur les machines découvertes.
- Une fois la découverte effectuée, vous regroupez les machines découvertes, puis vous exécutez les évaluations sur le groupe de votre choix à l’aide de l’appliance.

## <a name="azure-migrate-server-migration-tool"></a>Outil Migration de serveur Azure Migrate

L’outil Azure Migrate : migration de serveur vous aide à migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres machines virtualisées locales ainsi que des machines virtuelles de cloud public vers Azure. Vous pouvez migrer des machines après les avoir évaluées ou les migrer sans les avoir évaluées. 

Pour une migration sans agent des machines virtuelles VMware et des machines virtuelles Hyper-V, l’outil de migration de serveur utilise une appliance Azure Migrate que vous déployez localement. L’appliance est également utilisée si vous configurez l’évaluation de serveur. Cela est décrit dans la section précédente.


## <a name="selecting-assessmentmigration-tools"></a>Sélection des outils d’évaluation/de migration

Dans le hub Azure Migrate, vous sélectionnez l’outil à utiliser pour l’évaluation ou la migration, puis vous l’ajoutez à un projet Azure Migrate. Si vous ajoutez un outil d’un éditeur de logiciels indépendant ou Movere :

- Commencez par obtenir une licence ou par vous inscrire à un essai gratuit, conformément aux instructions de l’outil. Les licences des outils sont déterminées par l’ISV ou l’outil. 
- Dans chaque outil, il existe une option permettant de se connecter à Azure Migrate. Suivez les instructions de l’outil pour vous connecter.
- Suivez votre parcours de migration depuis le projet Azure Migrate sur tous les outils.


## <a name="movere"></a>Movere

Movere est une plateforme SaaS qui améliore les fonctionnalités décisionnelles en présentant avec précision des environnements informatiques complets en une seule journée. À mesure que les organisations se développent, évoluent et optimisent leurs outils numériques, la solution apporte aux entreprises la confiance dont elles ont besoin pour avoir une visibilité et un contrôle de leurs environnements, indépendamment de la plateforme, de l’application ou de la localisation géographique. Movere a été [acheté](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) par Microsoft et n’est plus vendu en tant qu’offre autonome.  Movere est disponible via les programmes « Microsoft Solution Assessment » et « Cloud Economics ». [En savoir plus](https://www.movere.io) sur Movere. 

Nous vous encourageons à examiner aussi Azure Migrate, notre service de migration intégré. Azure Migrate fournit un hub central pour simplifier votre migration vers le cloud. Le hub offre une prise en charge complète de différentes charges de travail, notamment des serveurs physiques et virtuels, des bases de données et des applications. La visibilité de bout en bout facilite le suivi de la progression tout au long de la découverte, de l’évaluation et de la migration. Avec les outils intégrés d’Azure et des éditeurs de logiciels indépendants partenaires, Azure Migrate a aussi un large éventail de fonctionnalités, notamment la découverte des serveurs virtuels et physiques, le dimensionnement adapté en fonction des performances, la planification des coûts, les évaluations basées sur l’importation et l’analyse des dépendances des applications sans agent. Si vous recherchez des conseils d’experts pour démarrer, laissez-vous guider par le programme [Fournisseur de services managés Azure Expert](https://azure.microsoft.com/partners) de Microsoft. Consultez le [site web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe deux versions du service Azure Migrate :

- **Version actuelle** : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. [Apprenez-en davantage](whats-new.md) sur les nouveautés de cette version.
- **Version précédente** : Si vous utilisiez la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge), vous devez désormais utiliser la version actuelle. Vous ne pouvez plus créer de projets Azure Migrate à l’aide de la version précédente, et nous vous recommandons de ne pas effectuer de nouvelles découvertes. Pour accéder à des projets existants, dans le portail Azure, recherchez et sélectionnez **Azure Migrate**. Le tableau de bord **Azure Migrate** affiche une notification et un lien permettant d’accéder aux anciens projets Azure Migrate.



## <a name="next-steps"></a>Étapes suivantes

- Essayez nos tutoriels pour évaluer des [machines virtuelles VMware](tutorial-prepare-vmware.md), des [machines virtuelles Hyper-V](tutorial-prepare-hyper-v.md) ou des [serveurs physiques](tutorial-prepare-physical.md).
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
