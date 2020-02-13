---
title: Questions courantes sur Azure Migrate
description: Obtenez des réponses aux questions courantes au sujet du service Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: baf01c0a0d5c6154305f7137c24deb0365b5e812
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062096"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum). Si vous avez d’autres questions, consultez les articles suivants :

- [Questions](common-questions-appliance.md) sur l’appliance Azure Migrate.
- [Questions](common-questions-discovery-assessment.md) sur la découverte, l’évaluation et la visualisation des dépendances.


## <a name="what-is-azure-migrate"></a>Qu’est-ce qu’Azure Migrate ?

Azure Migrate offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure d’applications et de charges de travail locales, ainsi que des machines virtuelles cloud privées/publiques. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers. [Plus d’informations](migrate-services-overview.md)


## <a name="what-can-i-do-with-azure-migrate"></a>Comment utiliser Azure Migrate ?

Utilisez Azure Migrate pour découvrir, évaluer et migrer des données, des applications et des infrastructures locales vers Azure. Azure Migrate prend en charge l’évaluation et la migration des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques, d’autres machines virtuelles virtualisées, des bases de données, des applications web et des bureaux virtuels de l’environnement local. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Quelle est la différence entre Azure Migrate et Site Recovery ?

Azure Migrate fournit un hub centralisé pour l’évaluation et la migration vers Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) est une solution de reprise d’activité. Azure Migrate : L’outil Migration de serveur utilise des fonctionnalités Site Recovery de back-end pour la migration lift-and-shift de certains ordinateurs locaux.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Quelle est la différence entre Azure Migrate Server Assessment et Map Toolkit ?

Server Assessment fournit une évaluation pour faciliter la préparation à la migration et l’évaluation des charges de travail pour la migration vers Azure. La boîte à outils [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) facilite d’autres tâches, notamment la planification de la migration pour les versions plus récentes de systèmes d’exploitation Windows clients et serveurs, ainsi que le suivi de l’utilisation des logiciels. Pour ces scénarios, continuez à utiliser la boîte à outils MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Quelle est la différence entre Server Assessment et le Planificateur de déploiement Site Recovery ?

Server Assessment est un outil de planification de la migration. Le Planificateur de déploiement Site Recovery est un outil de planification de la reprise d’activité après sinistre.

- **Planifier la migration locale vers Azure** : si vous envisagez de migrer vos serveurs locaux vers Azure, utilisez Server Assessment pour la planification de la migration. Il évalue les charges de travail locales, et fournit des conseils et des outils pour vous aider à effectuer la migration. Une fois le plan de migration en place, vous pouvez utiliser des outils, notamment Azure Migrate Server Migration, pour migrer les machines vers Azure.
- **Planifier la reprise d’activité après sinistre dans Azure** : si vous envisagez de configurer la reprise d’activité après sinistre d’un emplacement local vers Azure avec Site Recovery, utilisez le Planificateur de déploiement Site Recovery. Le planificateur de déploiement fournit une évaluation approfondie spécifique à Site Recovery de votre environnement local pour les besoins de la reprise d’activité après sinistre. Il fournit des recommandations relatives à la reprise d’activité, comme la réplication et le basculement.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Comment Server Migration fonctionne avec Site Recovery ?

- Si vous utilisez Azure Migrate : Server Migration pour effectuer une migration sans agent de machines virtuelles VMware locales, la migration est native à Azure Migrate et Site Recovery n’est pas utilisé.
- Si vous utilisez Azure Migrate : Server Migration pour effectuer une migration basée sur un agent de machines virtuelles VMware ou pour migrer des machines virtuelles Hyper-V ou des serveurs physiques, Azure Migrate Server Migration utilise le moteur de réplication Azure Site Recovery.


## <a name="which-geographies-are-supported"></a>Quelles sont les zones géographiques prises en charge ?

Passez en revue les zones géographiques prises en charge par Azure Migrate pour les [machines virtuelles VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) et pour les [machines virtuelles Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v).

## <a name="how-do-i-get-started"></a>Comment faire pour démarrer ?

Vous identifiez l’outil dont vous avez besoin, puis l’ajoutez à un projet Azure Migrate. Si vous ajoutez un outil d’un éditeur de logiciels indépendant ou Movere :
- Commencez par obtenir une licence ou par vous inscrire à un essai gratuit, conformément à la stratégie de l’outil. La gestion des licences pour les outils se fait conformément au modèle de licence de l’éditeur de logiciels indépendant ou de l’outil.
- Dans chaque outil, il existe une option permettant de se connecter à Azure Migrate. Suivez les instructions et la documentation de l’outil pour connecter celui-ci avec Azure Migrate.
Vous pouvez suivre de façon centralisée votre parcours de migration depuis le projet Azure Migrate, dans Azure et d’autres outils.

## <a name="how-do-i-delete-a-project"></a>Comment supprimer un projet ?

[Découvrez comment](how-to-delete-project.md) supprimer un projet. 




## <a name="next-steps"></a>Étapes suivantes
Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
