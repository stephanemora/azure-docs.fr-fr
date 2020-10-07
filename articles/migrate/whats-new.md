---
title: Nouveautés d’Azure Migrate
description: Découvrez les nouveautés et les mises à jour récentes du service Azure Migrate.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a46519d434061ebfdedbabe70a3e044cb69ca552
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322221"
---
# <a name="whats-new-in-azure-migrate"></a>Nouveautés d’Azure Migrate

[Azure Migrate](migrate-services-overview.md) vous aide à découvrir, à évaluer et à migrer localement des serveurs, applications et données vers le cloud Microsoft Azure. Cet article synthétise les nouvelles versions et fonctionnalités d’Azure Migrate.
## <a name="update-september-2020"></a>Mise à jour (septembre 2020)
- Azure Migrate vous permet désormais de migrer des serveurs vers des zones de disponibilité.
- Azure Migrate vous permet désormais de migrer des machines virtuelles UEFI et des serveurs physiques vers des machines virtuelles de 2e génération Azure. 

## <a name="update-august-2020"></a>Mise à jour (août 2020)

- Amélioration de l’expérience d’intégration quand une clé de projet Azure Migrate est générée à partir du portail, puis utilisée pour effectuer l’inscription de l’appliance.
- Option permettant de télécharger des fichiers OVA/VHD ou les scripts d’installation à partir du portail pour configurer les appliances VMware et Hyper-V respectivement.
- Mise à jour du Gestionnaire de configuration d’appliance pour une expérience utilisateur améliorée.
- Prise en charge de plusieurs informations d’identification pour la découverte des machines virtuelles Hyper-V.
- Amélioration des fonctionnalités de recherche, de tri et de filtre pour les informations d’identification et sources de découverte ajoutées.
- Options CSV d’entrée d’un seul élément, d’entrée de plusieurs éléments et d’importation afin que l’utilisateur puisse ajouter des sources de découverte pour des serveurs physiques et des hôtes/clusters Hyper-V.
- Amélioration de la gestion des erreurs avec les mises à jour d’état des opérations de validation et de découverte pour chaque source ajoutée dans la table. 

## <a name="update-june-2020"></a>Mise à jour (juin 2020)

- Les évaluations pour la migration de machines virtuelles VMware locales vers [Azure VMware Solution (AVS)](https://go.microsoft.com/fwlink/?linkid=2132637) sont désormais prises en charge. [En savoir plus](how-to-create-azure-vmware-solution-assessment.md)
- Prise en charge de plusieurs informations d’identification sur l’appliance pour la découverte des serveurs physiques.
- Prise en charge pour autoriser la connexion Azure à partir de l’appliance pour le locataire où la restriction du locataire a été configurée.


## <a name="update-april-2020"></a>Mise à jour (avril 2020)

Azure Migrate prend en charge les déploiements dans Azure Government. 

- Vous pouvez découvrir et évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V et des serveurs physiques.
- Vous pouvez migrer des machines virtuelles Hyper-V, des machines virtuelles VMware et des serveurs physiques vers Azure.
- Pour la migration VMware, vous pouvez utiliser la migration sans agent ou basée sur un agent. [Plus d’informations](server-migrate-overview.md)
- [Passez en revue](migrate-support-matrix.md#supported-geographies-azure-government) les zones géographiques et régions prises en charge pour Azure Government.
- L’[analyse des dépendances basée sur les agents](concepts-dependency-visualization.md#agent-based-analysis) n’est pas prise en charge dans Azure Government.
- Les fonctionnalités d’évaluation sont prises en charge dans Azure Government, plus spécifiquement l’[analyse des dépendances sans agent](concepts-dependency-visualization.md#agentless-analysis) et la [découverte d’application](how-to-discover-applications.md).


## <a name="update-march-2020"></a>Mise à jour (mars 2020)

Une installation basée sur un script est désormais disponible pour configurer l’[appliance Azure Migrate](migrate-appliance.md) :

- L’installation basée sur un script est une alternative à l’installation .OVA (VMware)/VHD (Hyper-V) de l’appliance.
- Vous disposez d’un script de programme d’installation PowerShell qui permet de configurer l’appliance pour VMware/Hyper-V sur une machine existante exécutant Windows Server 2016.

## <a name="update-november-2019"></a>Mise à jour (novembre 2019)

Un certain nombre de nouvelles fonctionnalités ont été ajoutées à Azure Migrate :

- **Évaluation des serveurs physiques**. L’évaluation des serveurs physiques locaux est désormais prise en charge, en plus de la migration du serveur physique qui est déjà prise en charge.
- **Évaluation basée sur l’importation**. L’évaluation des machines à l’aide des métadonnées et des données de performances fournies dans un fichier CSV est désormais prise en charge.
- **Découverte des applications** : Azure Migrate prend désormais en charge la découverte au niveau de l’application des applications, des rôles et des fonctionnalités à l’aide de l’appliance Azure Migrate. Ceci est actuellement pris en charge pour les machines virtuelles VMware uniquement et est limité à la détection uniquement (l’évaluation n’est pas actuellement prise en charge). [En savoir plus](how-to-discover-applications.md)
- **Visualisation des dépendances sans agent** : Vous n’avez plus besoin d’installer explicitement des agents pour la visualisation des dépendances. La visualisation sans agent et basée sur l’agent sont désormais prises en charge.
- **Bureau virtuel** : Utilisez les outils ISV pour évaluer et migrer une infrastructure de bureau virtuel locale (VDI) vers Windows Virtual Desktop dans Azure.
- **Application web** : L’assistant Migration Azure App Service, utilisé pour l’évaluation et la migration d’applications web, est désormais intégré à Azure Migrate.

De nouveaux outils d’évaluation et de migration ont été ajoutés à Azure Migrate :

- **Rackware** : Offre de migration cloud.
- **Movere** : Offre d’évaluation.

[Découvrez plus d’informations ](migrate-services-overview.md) sur l’utilisation des outils et des offres ISV pour l’évaluation et la migration dans Azure Migrate.

## <a name="azure-migrate-current-version"></a>Version actuelle d’Azure Migrate

La version actuelle d’Azure Migrate (publiée en juillet 2019) propose un certain nombre de nouvelles fonctionnalités :

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

## <a name="azure-migrate-previous-version"></a>Version précédente d’Azure Migrate

Si vous utilisez la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge), vous devez désormais utiliser la version actuelle. La version précédente ne permet plus de créer des projets Azure Migrate ou d’effectuer de nouvelles découvertes. Vous pouvez toujours accéder aux projets existants. Pour ce faire, dans le portail Azure > **Tous les services**, recherchez **Azure Migrate**. Les notifications Azure Migrate contiennent un lien permettant d’accéder aux anciens projets Azure Migrate.



## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
- Essayez nos didacticiels pour évaluer les [machines virtuelles VMware](tutorial-assess-vmware.md) et les [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
