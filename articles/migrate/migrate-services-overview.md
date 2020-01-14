---
title: À propos d’Azure Migrate
description: Découvrez le service Azure Migrate.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: 983ba420067bf2e4b6416287d8898943142c90c0
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563562"
---
# <a name="about-azure-migrate"></a>À propos d’Azure Migrate

Cet article donne une vue d’ensemble du service Azure Migrate.

Utilisez Azure Migrate pour migrer vers Azure. Azure Migrate offre un hub centralisé pour évaluer et migrer une infrastructure, des applications et des données locales vers Azure, avec les fonctionnalités suivantes :

- **Plateforme de migration unifiée** : Un portail unique pour démarrer, exécuter et suivre votre parcours de migration vers Azure.
- **Panoplie d’outils** : Panoplie d’outils d’évaluation et de migration. Le hub comprend Azure Migrate : Server Assessment et Azure Migrate : Server Migration. Il s’intègre à d’autres services Azure, à d’autres outils et à des offres d’éditeurs de logiciels indépendants.
- **Évaluation et migration** : dans le hub Azure Migrate, vous pouvez évaluer et migrer les éléments suivants :
    - **Serveurs** : évaluez et migrez des serveurs locaux vers des machines virtuelles Azure.
    - **Bases de données** : évaluez et migrez des bases de données locales vers Azure SQL DB ou vers Azure SQL Managed Instance.
    - **Applications web** : évaluez et migrez des applications web locales vers Azure App Service à l’aide de l’Assistant Azure App Service.
    - **Bureaux virtuels** : évaluez et migrez votre infrastructure de bureau virtuel locale (VDI) vers Windows Virtual Desktop dans Azure.
    - **Données** : migrez rapidement et à moindre coût de grandes quantités de données vers Azure à l’aide des produits Azure Data Box. 


## <a name="integrated-tools"></a>Outils intégrés

Le Hub Azure Migrate fournit les outils suivants.

**Outil** | **Évaluer/migrer** | **Détails**
--- | --- | ---
**Azure Migrate : Server Assessment** | Évaluer des serveurs | Découvrez et évaluez des machines virtuelles VMware et Hyper-V ainsi que des serveurs physiques locaux afin de déterminer s’ils sont prêts pour une migration vers Azure.
**Azure Migrate : Server Migration** | Migrer des serveurs | Migrez des machines virtuelles VMware et Hyper-V ainsi que des serveurs physiques locaux, d’autres machines virtualisées et des machines virtuelles de cloud public vers Azure. 
**Assistant Migration de données Microsoft (DMA)** | Évaluez les bases de données SQL Server locales pour la migration vers Azure SQL DB, Azure SQL Managed Instance ou des machines virtuelles Azure exécutant SQL Server. | DMA fournit des informations sur les problèmes de blocage potentiels pour la migration. Il identifie les fonctionnalités non prises en charge, ainsi que de nouvelles fonctionnalités dont vous pourrez tirer bénéficier après la migration, et vous aide à identifier le chemin d’accès approprié pour la migration de base de données. [Plus d’informations](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)
**Database Migration Service (DMS)** | Migrez des bases de données locales vers des machines virtuelles Azure exécutant SQL, Azure SQL DB et Azure SQL Managed instances. | [Découvrez-en plus](https://docs.microsoft.com/azure/dms/dms-overview) sur DMS.
**Movere** | Évaluer des serveurs | [En savoir plus](#movere) sur Movere.
**Assistant Migration d’applications web** | Évaluez et migrez des applications web locales vers Azure. |  Utilisez l’Assistant Migration Azure App Service pour évaluer les sites web locaux en vue de leur migration vers Azure App Service.<br/><br/> migrez des applications web .NET et PHP vers Azure à l’aide de l’Assistant Migration Azure App Service. [En savoir plus](https://appmigration.microsoft.com/) sur l’Assistant.
**Azure Data Box** | Migration de données hors connexion. | Pour déplacer de grandes quantités de données hors connexion vers Azure, utilisez la gamme de produits Azure Data Box. [Plus d’informations](https:/docs.microsoft.com/azure/databox/)

## <a name="isv-integration"></a>Intégration d’éditeurs de logiciels indépendants

Azure Migrate s’intègre à un certain nombre d’offres d’éditeurs de logiciels indépendants. 

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


## <a name="azure-migrate-server-assessment-tool"></a>Outil Azure Migrate : Server Assessment

L’outil Azure Migrate : Server Assessment découvre et évalue des machines virtuelles VMware et Hyper-V ainsi que des serveurs physiques locaux en vue de leur migration vers Azure. Il vous aide à identifier les aspects suivants :

- **État de préparation pour Azure :** évaluez si les machines locales sont prêtes pour la migration vers Azure.
- **Dimensionnement d’Azure :** taille estimée des machines virtuelles Azure après la migration.
- **Estimation des coûts Azure :** coûts estimés liés à l’exécution de serveurs locaux dans Azure.
- **Visualisation des dépendances :** Si vous utilisez Server Assessment avec la visualisation des dépendances, vous pouvez identifier efficacement les dépendances entre serveurs et les méthodes optimales pour déplacer des serveurs dépendants vers Azure.

L’outil Évaluation de serveur utilise une appliance légère que vous déployez localement et inscrivez avec l’outil.

- L’appliance s’exécute sur un serveur physique ou une machine virtuelle, et est facilement installée à l’aide d’un modèle téléchargé.
- L’appliance découvre les machines locales et envoie en continu les métadonnées et les données de performances des machines à Azure Migrate.
- La découverte de l’appliance se fait sans agent. Rien ne doit explicitement être installé sur les machines découvertes.
- Après la découverte, vous rassemblez les machines découvertes dans des groupes et évaluez ces derniers en vue de la migration.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate : Outil Server Migration

L’outil Azure Migrate : Server Migration vous aide à migrer des machines virtuelles VMware et Hyper-V ainsi que des serveurs physiques locaux, d’autres machines virtualisées et des machines virtuelles de cloud public vers Azure. Vous pouvez migrer des machines après les avoir évaluées ou les migrer sans les avoir évaluées.


## <a name="select-a-tool"></a>Sélectionner un outil

Dans le hub Azure Migrate, sélectionnez l’outil que vous souhaitez utiliser pour l’évaluation, puis ajoutez-le à un projet Azure Migrate. Si vous ajoutez un outil d’un éditeur de logiciels indépendant ou Movere :

- Commencez par obtenir une licence ou par vous inscrire à un essai gratuit, conformément aux instructions de l’outil. Les licences des outils sont déterminées par l’ISV/l’outil. 
- Dans chaque outil, il existe une option permettant de se connecter à Azure Migrate. Suivez les instructions pour vous connecter.
- Suivez votre parcours de migration depuis le projet Azure Migrate, sur l’ensemble des outils.


## <a name="movere"></a>Movere

Movere est une plateforme SaaS qui améliore les fonctionnalités décisionnelles en présentant avec précision des environnements informatiques complets en une seule journée. À mesure que les organisations se développent, évoluent et optimisent leur numérisation, la solution offre aux entreprises la confiance dont elles ont besoin pour avoir une visibilité et un contrôle de leurs environnements, quelle que soit la plateforme, l’application ou l’emplacement géographique. Movere a été [acheté](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) par Microsoft et n’est plus vendu en tant qu’offre autonome.  Movere est disponible via les programmes « Microsoft Solution Assessment » et « Cloud Economics ». [En savoir plus](https://www.movere.io) sur Movere. Si vous avez des questions, envoyez-les à l’adresse movereq@microsoft.com ou contactez votre représentant Microsoft.

Nous vous encourageons à examiner aussi Azure Migrate, notre service de migration intégré. Azure Migrate fournit un hub central pour simplifier votre migration vers le cloud. Le hub offre une prise en charge complète de différentes charges de travail, notamment des serveurs physiques et virtuels, des bases de données et des applications. La visibilité de bout en bout facilite le suivi de la progression tout au long de la découverte, de l’évaluation et de la migration. Avec les outils intégrés d’Azure et des éditeurs de logiciels indépendants partenaires, Azure Migrate a aussi un large éventail de fonctionnalités, notamment la découverte des serveurs virtuels et physiques, le dimensionnement adapté en fonction des performances, la planification des coûts, les évaluations basées sur l’importation et l’analyse des dépendances des applications sans agent. Si vous recherchez des conseils d’experts pour commencer, Microsoft a des partenariats avec des [fournisseurs de services managés spécialisés Azure](https://azure.microsoft.com/partners) compétents pour vous guider dans cette démarche. Consultez le [site web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe deux versions du service Azure Migrate :

- **Version actuelle** : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. [Apprenez-en davantage](whats-new.md) sur les nouveautés de cette version.
- **Version précédente** : Si vous utilisiez la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge), vous devez désormais utiliser la version actuelle. Vous ne pouvez plus créer de projets Azure Migrate à l’aide de la version précédente, et nous vous recommandons de ne pas effectuer de nouvelles découvertes. Pour accéder à des projets existants, dans le portail Azure, recherchez et sélectionnez **Azure Migrate**. Le tableau de bord **Azure Migrate** affiche une notification et un lien permettant d’accéder aux anciens projets Azure Migrate.



## <a name="next-steps"></a>Étapes suivantes

- Essayez nos tutoriels pour évaluer des [machines virtuelles VMware](tutorial-prepare-vmware.md), des [machines virtuelles Hyper-V](tutorial-prepare-hyper-v.md) et des [serveurs physiques](tutorial-prepare-physical.md).
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
