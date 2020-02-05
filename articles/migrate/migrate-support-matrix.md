---
title: Tableau de prise en charge Azure Migrate
description: Fournit un résumé des limitations et des paramètres de prise en charge pour le service Azure Migrate.
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 5c29b80f30b024d34ec4e8f65e51b59fc70e8f93
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846549"
---
# <a name="azure-migrate-support-matrix"></a>Tableau de prise en charge Azure Migrate

Vous pouvez utiliser le [service Azure Migrate](migrate-overview.md) pour évaluer et migrer des machines vers le cloud Microsoft Azure. Cet article énumère les limitations et les paramètres généraux de prise en charge pour les scénarios et les déploiements Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Scénarios d’évaluation et de migration pris en charge

Le tableau récapitule les scénarios de détection, d’évaluation et de migration pris en charge.

**Déploiement** | **Détails** 
--- | --- 
**Détection spécifique de l’application** | Vous pouvez détecter des applications, des rôles et des fonctionnalités qui s’exécutent sur des machines virtuelles VMware. Actuellement, cette fonctionnalité est limitée à la détection. L’évaluation est actuellement au niveau de la machine. Nous n’offrons pas encore d’évaluation spécifique d’une application, d’un rôle ou d’une fonctionnalité. 
**Évaluation locale** | Évaluez les charges de travail et les données locales qui s’exécutent sur des machines virtuelles VMware, des machines virtuelles Hyper-V et des serveurs physiques. Évaluez en utilisant Azure Migrate Server Assessment et l’Assistant Migration de données Microsoft ainsi que d’autres outils et offres ISV.
**Migration locale vers Azure** | Migrez vers Azure des charges de travail et données exécutées sur des serveurs physiques, des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des machines virtuelles cloud vers Azure. Migrez en utilisant Azure Migrate Server Assessment et Azure Database Migration Service ainsi que d’autres outils et offres ISV.


## <a name="supported-tools"></a>Outils pris en charge

La prise en charge d’outils spécifiques est récapitulée ci-dessous.

**Outil** | **Évaluer** | **Migrer** 
--- | --- | ---
Évaluation de serveur Azure Migrate | Évaluer des [machines virtuelles VMware](tutorial-prepare-vmware.md), des [machines virtuelles Hyper-V](tutorial-prepare-hyper-v.md) et des [serveurs physiques](tutorial-prepare-physical.md). |  Non disponible (N/D)
Migration de serveur Azure Migrate | N/D | Migrer des [machines virtuelles VMware](tutorial-migrate-vmware.md), des [machines virtuelles Hyper-V](tutorial-migrate-hyper-v.md) et des [serveurs physiques](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N/D | Migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des charges de travail de cloud public. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des charges de travail de cloud public. | N/D
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Évaluer et migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des charges de travail de cloud public. |  Migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des charges de travail de cloud public.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des charges de travail de cloud public.| N/D
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Évaluer des bases de données SQL Server locales. | N/D
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | N/D | Migrer SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Évaluer Virtual Desktop Infrastructure (VDI) | N/D
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Évaluer des machines virtuelles VMWare, des machines virtuelles Hyper-V, des machines virtuelles Xen, des machines physiques, des stations de travail (y compris VDI) et des charges de travail de cloud public | N/D
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N/D | Migrer des machines virtuelles VMWare, des machines virtuelles Hyper-V, des machines virtuelles Xen, des machines virtuelles KVM, des machines physiques, des charges de travail de cloud public 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des charges de travail de cloud public. | N/D
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques, des charges de travail de cloud public et des bases de données SQL Server. | N/D
[Assistant Migration d’applications web](https://appmigration.microsoft.com/) | Évaluer des applications web | Migrer des applications web.


## <a name="azure-migrate-projects"></a>Projets Azure Migrate

**Support** | **Détails**
--- | ---
Subscription | Vous pouvez avoir plusieurs projets Azure Migrate par abonnement.
Autorisations Azure | Vous avez besoin d’autorisations Contributeur ou Propriétaire dans l’abonnement pour créer un projet Azure Migrate.
Machines virtuelles VMware  | Évaluez jusqu’à 35 000 machines virtuelles VMware dans un même projet.
Machines virtuelles Hyper-V | Évaluez jusqu'à 35 000 machines virtuelles Hyper-V au sein d'un même projet.

Un projet peut inclure à la fois des machines virtuelles VMware et des machines virtuelles Hyper-V, jusqu’aux limites d’évaluation.

## <a name="azure-permissions"></a>Autorisations Azure

Pour utiliser Azure Migrate avec Azure, vous devez disposer de ces autorisations avant d'évaluer et de migrer des machines.

**Tâche** | **autorisations** | **Détails**
--- | --- | ---
Créer un projet Azure Migrate | Votre compte Azure doit être autorisé à créer un projet. | Configurez [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project) ou des [serveurs physiques](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Inscrire l’appliance Azure Migrate | Azure Migrate utilise une [appliance Azure Migrate](migrate-appliance.md) légère pour évaluer les machines virtuelles VMware avec Azure Migrate Server Assessment et exécuter la [migration sans agent](server-migrate-overview.md) des machines virtuelles VMware avec Azure Migrate Server Migration. Cette appliance effectue la découverte des machines virtuelles et envoie les métadonnées et les données de performances des machines virtuelles à Azure Migrate.<br/><br/> Durant l’inscription, Azure Migrate crée deux applications Azure AD (Azure Active Directory) qui identifient cette appliance de façon unique et doit être autorisé à créer ces applications.<br/><br/> - La première application communique avec les points de terminaison de service Azure Migrate.<br/><br/> - La seconde application accède à un coffre de clés Azure Key Vault créé pendant l’inscription pour stocker les informations sur l’application Azure AD et les paramètres de configuration de l’appliance. | Configurez [VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance) ou des [serveurs physiques](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Créer un coffre de clés pour la migration sans agent VMware | Pour migrer des machines virtuelles VMware avec Azure Migrate Server Migration sans agent, Azure Migrate crée un coffre de clés afin de gérer les clés d’accès au compte de stockage de réplication de votre abonnement. Pour créer ce coffre, vous devez définir des autorisations (propriétaire, collaborateur et administrateur de l’accès utilisateur) sur le groupe de ressources dans lequel réside le projet Azure Migrate. | [Définissez](tutorial-prepare-vmware.md#assign-role-assignment-permissions) des autorisations.

## <a name="supported-geographies"></a>Zones géographiques prises en charge

Vous pouvez créer un projet Azure Migrate dans un certain nombre de zones géographiques. Même si vous ne pouvez créer des projets que dans ces zones, vous pouvez néanmoins évaluer ou migrer des machines pour d’autres emplacements cibles. La zone géographique du projet est uniquement utilisée pour stocker les métadonnées détectées.

**Zone géographique** | **Emplacement de stockage des métadonnées**
--- | ---
Azure Government | Gouvernement américain - Virginie
Asie-Pacifique | Asie Est ou Asie Sud-Est
Australie | Australie Est ou Australie Sud-Est
Brésil | Brésil Sud
Canada | Canada Centre ou Canada Est
Europe | Europe Nord ou Europe Ouest
France | France Centre
Inde | Inde Centre ou Inde Sud
Japon |  Japon Est ou Japon Ouest
Corée du Sud | Corée Centre ou Corée Sud
United Kingdom | Royaume-Uni Sud ou Royaume-Uni Ouest
États-Unis | USA Centre ou USA Ouest 2


 > [!NOTE]
 > La prise en charge d’Azure Government est actuellement disponible pour l’[ancienne version](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) d’Azure Migrate uniquement.



## <a name="vmware-assessment-and-migration"></a>Évaluation et migration VMware

[Consultez](migrate-support-matrix-vmware.md) la matrice de prise en charge de l’évaluation de serveur et de la migration de serveur Azure Migrate pour les machines virtuelles VMware.

## <a name="hyper-v-assessment-and-migration"></a>Évaluation et migration Hyper-V

[Consultez](migrate-support-matrix-hyper-v.md) la matrice de prise en charge de l’évaluation de serveur et de la migration de serveur Azure Migrate pour les machines virtuelles Hyper-V.



## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe deux versions du service Azure Migrate :

- **Version actuelle** : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. [Plus d’informations](whats-new.md#release-version-july-2019)
- **Version précédente** : les clients utilisant la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge) doivent à présent utiliser la version actuelle. La version précédente ne permet plus de créer des projets Azure Migrate ni d’effectuer de nouvelles découvertes.

## <a name="next-steps"></a>Étapes suivantes

- [Évaluez les machines virtuelles VMware](tutorial-assess-vmware.md) pour la migration.
- [Évaluez les machines virtuelles Hyper-V](tutorial-assess-hyper-v.md) pour la migration.

