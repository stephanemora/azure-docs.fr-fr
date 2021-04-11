---
title: Tableau de prise en charge Azure Migrate
description: Fournit un résumé des limitations et des paramètres de prise en charge pour le service Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: b9ea447b0204ad91065f27d265584c8787167fc2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870719"
---
# <a name="azure-migrate-support-matrix"></a>Tableau de prise en charge Azure Migrate

Vous pouvez utiliser le [service Azure Migrate](./migrate-services-overview.md) pour évaluer et migrer des serveurs vers le cloud Microsoft Azure. Cet article énumère les limitations et les paramètres généraux de prise en charge pour les scénarios et les déploiements Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Scénarios d’évaluation et de migration pris en charge

Le tableau récapitule les scénarios de détection, d’évaluation et de migration pris en charge.

**Déploiement** | **Détails** 
--- | --- 
**Découverte** | Vous pouvez découvrir des métadonnées de serveur et des données de performances dynamiques.
**App-discovery** | Vous pouvez détecter des applications, des rôles et des fonctionnalités qui s’exécutent sur des machines virtuelles VMware. Actuellement, cette fonctionnalité est limitée à la détection. L’évaluation est actuellement au niveau du serveur. Nous n’offrons pas encore d’évaluations basées sur une application, un rôle ou une fonctionnalité. 
**Évaluation** | Évaluez les charges de travail et les données locales qui s’exécutent sur des machines virtuelles VMware, des machines virtuelles Hyper-V et des serveurs physiques. Évaluer à l’aide d’Azure Migrate : Server Assessment, Assistant Migration de données Microsoft (DMA) ainsi que d’autres outils et offres ISV.
**Migration** | Migrez vers Azure des charges de travail et données exécutées sur des serveurs physiques, des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des machines virtuelles cloud vers Azure. Effectuez des évaluations en utilisant Azure Migrate : Server Assessment et Azure Database Migration Service (DMS) ainsi que d’autres outils et offres ISV.

> [!NOTE]
> Actuellement, les outils ISV ne peuvent pas envoyer de données à Azure Migrate dans Azure Government. Vous pouvez utiliser les outils intégrés de Microsoft, ou utiliser les outils des partenaires de façon indépendante.

## <a name="supported-tools"></a>Outils pris en charge


La prise en charge d’outils spécifiques est récapitulée ci-dessous.

**Outil** | **Évaluer** | **Migrer** 
--- | --- | ---
Azure Migrate : Server Assessment | Évaluer des [machines virtuelles VMware](./tutorial-discover-vmware.md), des [machines virtuelles Hyper-V](./tutorial-discover-hyper-v.md) et des [serveurs physiques](./tutorial-discover-physical.md). |  Non disponible (N/D)
Azure Migrate : Server Migration | N/A | Migrer des [machines virtuelles VMware](tutorial-migrate-vmware.md), des [machines virtuelles Hyper-V](tutorial-migrate-hyper-v.md) et des [serveurs physiques](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N/A | Migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres charges de travail de cloud. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres charges de travail de cloud. | N/A
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres charges de travail de cloud. |  Migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des charges de travail de cloud public.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres charges de travail de cloud.| N/A
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | Évaluer des bases de données SQL Server. | N/A
[DMS](../dms/dms-overview.md) | N/A | Migrer SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Évaluer Virtual Desktop Infrastructure (VDI) | N/A
[Movere](https://www.movere.io/) | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des machines virtuelles Xen, des serveurs physiques, des stations de travail (y compris VDI) et d’autres charges de travail de cloud. | N/A
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N/A | Migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des machines virtuelles Xen, des serveurs physiques et d’autres charges de travail de cloud 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres charges de travail de cloud. | N/A
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Évaluer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres charges de travail de cloud, et des bases de données SQL Server. | N/A
[Assistant Migration d’applications web](https://appmigration.microsoft.com/) | Évaluer des applications web | Migrer des applications web.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | N/A |  Migrer des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et d’autres charges de travail de cloud.


## <a name="project"></a>Project

**Support** | **Détails**
--- | ---
Abonnement | Peut avoir plusieurs projets dans un abonnement.
Autorisations Azure | L’utilisateur a besoin d’autorisations Contributeur ou Propriétaire dans l’abonnement pour créer un projet.
Machines virtuelles VMware  | Évaluez jusqu’à 35 000 machines virtuelles VMware dans un même projet.
Machines virtuelles Hyper-V    | Évaluez jusqu'à 35 000 machines virtuelles Hyper-V au sein d'un même projet.

Un projet peut inclure à la fois des machines virtuelles VMware et des machines virtuelles Hyper-V, jusqu’aux limites d’évaluation.

## <a name="azure-permissions"></a>Autorisations Azure

Pour utiliser Azure Migrate avec Azure, vous devez disposer de ces autorisations avant d'évaluer et de migrer des serveurs.

**Tâche** | **autorisations** | **Détails**
--- | --- | ---
Création d’un projet | Votre compte Azure doit être autorisé à créer un projet. | Configurez [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) ou des [serveurs physiques](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Inscrire l’appliance Azure Migrate| Azure Migrate utilise une [appliance Azure Migrate](migrate-appliance.md) légère pour évaluer les serveurs avec Azure Migrate : Server Assessment ainsi que pour exécuter une [migration sans agent](server-migrate-overview.md) des machines virtuelles VMware avec Azure Migrate : Server Migration. Cette appliance effectue la découverte des serveurs, puis envoie les métadonnées et les données de performances à Azure Migrate.<br/><br/> Au cours du processus, les fournisseurs d’inscription (Microsoft.OffAzure, Microsoft.Migrate et Microsoft.KeyVault) sont inscrits auprès de l’abonnement choisi dans l’appliance pour permettre à cet abonnement de fonctionner avec le fournisseur de ressources. Pour vous inscrire, vous devez disposer d’un accès Contributeur ou Propriétaire à l’abonnement.<br/><br/> **VMware** - Durant l’intégration, Azure Migrate crée deux applications Azure Active Directory (Azure AD). La première application assure la communication entre les agents de l’appliance et le service Azure Migrate. L'application ne dispose pas des autorisations nécessaires pour appeler la fonctionnalité de gestion des ressources Azure, ou ne dispose pas de l'accès Azure RBAC aux ressources. La seconde application accède à un coffre de clés Azure Key Vault créé dans l’abonnement de l’utilisateur pour une migration VMware sans agent uniquement. Dans une migration sans agent, Azure Migrate crée un coffre de clés pour gérer les clés d’accès au compte de stockage de réplication de votre abonnement. Il dispose de l'accès Azure RBAC au coffre de clés Azure Key Vault (dans le locataire du client) lorsque la détection est lancée à partir de l'appliance.<br/><br/> **Hyper-V** - Durant l’intégration, Azure Migrate crée une seule application Azure AD. L’application assure la communication entre les agents d’appliance et le service Azure Migrate. L'application ne dispose pas des autorisations nécessaires pour appeler la fonctionnalité de gestion des ressources Azure, ou ne dispose pas de l'accès Azure RBAC aux ressources. | Configurez [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) ou des [serveurs physiques](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Créer un coffre de clés pour la migration sans agent VMware | Pour migrer des machines virtuelles VMware avec Azure Migrate: Server Migration, Azure Migrate crée un coffre de clés pour gérer les clés d’accès au compte de stockage de réplication de votre abonnement. Pour créer le coffre, vous configurez des autorisations (Propriétaire ou Contributeur et Administrateur de l’accès utilisateur) pour le groupe de ressources où réside le projet. | [Définissez](./tutorial-discover-vmware.md#prepare-an-azure-user-account) des autorisations.

## <a name="supported-geographies-public-cloud"></a>Zones géographiques prises en charge (cloud public)

Vous pouvez créer un projet dans un certain nombre de zones géographiques dans le cloud public.

- Même si vous ne pouvez créer des projets que dans ces zones, vous pouvez néanmoins évaluer ou migrer des serveurs pour d’autres emplacements cibles.
- La zone géographique du projet est uniquement utilisée pour stocker les métadonnées détectées.
- Lorsque vous créez un projet, vous sélectionnez une zone géographique. Le projet et les ressources associées sont créés dans l’une des régions de la zone géographique. La région est allouée par le service Azure Migrate.

**Zone géographique** | **Emplacement de stockage des métadonnées**
--- | ---
Asie-Pacifique | Asie Est ou Asie Sud-Est
Australie | Australie Est ou Australie Sud-Est
Brésil | Brésil Sud
Canada | Canada Centre ou Canada Est
Europe | Europe Nord ou Europe Ouest
France | France Centre
Inde | Inde Centre ou Inde Sud
Japon |  Japon Est ou Japon Ouest
Corée du Sud | Corée Centre ou Corée Sud
Suisse | Suisse Nord
Royaume-Uni | Royaume-Uni Sud ou Royaume-Uni Ouest
États-Unis | USA Centre ou USA Ouest 2

> [!NOTE]
> Pour la géographie de la Suisse, Suisse Ouest n’est disponible que pour les utilisateurs de l’API REST et nécessite un abonnement approuvé.

## <a name="supported-geographies-azure-government"></a>Zones géographiques prises en charge (Azure Government)

**Tâche** | **Zone géographique** | **Détails**
--- | --- | ---
Créer un projet | États-Unis | Les métadonnées sont stockées dans les régions US Gov Arizona et US Gov Virginie
Évaluation cible | États-Unis | Régions cibles : US Gov Arizona, US Gov Virginie, US Gov Texas
Réplication cible | États-Unis | Régions cibles : US DoD Centre, US DoD Est, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginie


## <a name="vmware-assessment-and-migration"></a>Évaluation et migration VMware

[Passez en revue](migrate-support-matrix-vmware.md) la prise en charge de l’outil Azure Migrate: Server Assessment et Azure Migrate : Server Migration pour la migration de machines virtuelles VMware.

## <a name="hyper-v-assessment-and-migration"></a>Évaluation et migration Hyper-V

[Passez en revue](migrate-support-matrix-hyper-v.md) l’outil Azure Migrate: Server Assessment et Azure Migrate : Server Migration pour la migration de machines virtuelles Hyper-V.



## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe deux versions du service Azure Migrate :

- **Version actuelle** : utilisez cette version pour créer des projets, découvrir des machines locales et orchestrer des évaluations et migrations. [Plus d’informations](whats-new.md)
- **Version précédente** : les clients utilisant la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge) doivent à présent utiliser la version actuelle. La version précédente ne permet plus de créer des projets ni d’effectuer de nouvelles découvertes.

## <a name="next-steps"></a>Étapes suivantes

- [Évaluez les machines virtuelles VMware](./tutorial-assess-vmware-azure-vm.md) pour la migration.
- [Évaluez les machines virtuelles Hyper-V](tutorial-assess-hyper-v.md) pour la migration.
