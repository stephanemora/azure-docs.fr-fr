---
title: Tableau de prise en charge Azure Migrate
description: Fournit un résumé des limitations et des paramètres de prise en charge pour le service Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 3fb36eb9b8507903d1aee00b82c375685a4dfa6c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279469"
---
# <a name="azure-migrate-support-matrix"></a>Tableau de prise en charge Azure Migrate

Vous pouvez utiliser le [service Azure Migrate](migrate-overview.md) pour évaluer et migrer des machines vers le cloud Microsoft Azure. Cet article énumère les limitations et les paramètres généraux de prise en charge pour les scénarios et les déploiements Azure Migrate.


## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe deux versions du service Azure Migrate :

- **Version actuelle** : utilisez cette version pour créer des projets Azure Migrate, découvrir des machines locales et orchestrer des évaluations et migrations. [Plus d’informations](whats-new.md#azure-migrate-new-version)
- **Version précédente** : les clients utilisant la version précédente d’Azure Migrate (seule l’évaluation des machines virtuelles VMware locales était prise en charge) doivent à présent utiliser la version actuelle. La version précédente ne permet plus de créer des projets Azure Migrate ni d’effectuer de nouvelles découvertes.

## <a name="supported-migration-scenarios"></a>Scénarios de migration pris en charge

Le tableau récapitule les scénarios de migration pris en charge.

**Déploiement** | **Détails** 
--- | --- 
**Évaluation locale** | Évaluez les charges de travail et les données locales exécutées sur des machines virtuelles VMware et des machines virtuelles Hyper-V. Évaluez à l’aide de la fonctionnalité Évaluation de serveur Azure Migrate et de l’Assistant Migration de données Microsoft, ainsi que d’outils tiers, notamment Cloudamize, Corent Tech et Turbonomic Server.
**Migration locale vers Azure** | Migrez vers Azure des charges de travail et données exécutées sur des serveurs physiques, des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques et des machines virtuelles cloud vers Azure. Migrez à l’aide de la fonctionnalité Évaluation de serveur Azure Migrate et d’Azure Database Migration Service (DMS) ainsi que d’outils tiers, notamment Carbonite et CorentTech.

La prise en charge d’outils spécifiques est résumée ci-dessous.

**Outil** | **Évaluation/migration** | **Détails**
--- | --- | ---
Évaluation de serveur Azure Migrate | Évaluation | Essayez l’évaluation de serveur pour [Hyper-V](tutorial-prepare-hyper-v.md) et [VMware](tutorial-prepare-vmware.md).
Cloudamize | Évaluation | [Plus d’informations](https://www.cloudamize.com/platform#tab-0)
CorentTech | Évaluation | [Plus d’informations](https://www.corenttech.com/)
Turbonomic | Évaluation | [Plus d’informations](https://turbonomic.com/solutions/technologies/azure-cloud/)
Migration de serveur Azure Migrate | Migration | Essayez la migration de serveur pour [Hyper-V](tutorial-migrate-hyper-v.md) et [VMware](tutorial-migrate-vmware.md).
Carbonite | Migration | [Plus d’informations](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)
CorentTech | Migration | [Plus d’informations](https://www.corenttech.com/)


## <a name="azure-migrate-projects"></a>Projets Azure Migrate

**Support** | **Détails**
--- | ---
Subscription | Vous pouvez avoir plusieurs projets Azure Migrate par abonnement.
Autorisations Azure | Vous avez besoin d’autorisations Contributeur ou Propriétaire dans l’abonnement pour créer un projet Azure Migrate.
Machines virtuelles VMware  | Évaluez jusqu’à 35 000 machines virtuelles VMware dans un même projet.
Machines virtuelles Hyper-V | Évaluez jusqu'à 35 000 machines virtuelles Hyper-V au sein d'un même projet.

Un projet peut inclure à la fois des machines virtuelles VMware et des machines virtuelles Hyper-V, jusqu’aux limites d’évaluation.


## <a name="vmware-assessment-and-migration"></a>Évaluation et migration VMware

[Consultez](migrate-support-matrix-vmware.md) la matrice de prise en charge de l’évaluation de serveur et de la migration de serveur Azure Migrate pour les machines virtuelles VMware.

## <a name="hyper-v-assessment-and-migration"></a>Évaluation et migration Hyper-V

[Consultez](migrate-support-matrix-hyper-v.md) la matrice de prise en charge de l’évaluation de serveur et de la migration de serveur Azure Migrate pour les machines virtuelles Hyper-V.


## <a name="next-steps"></a>Étapes suivantes

- [Évaluez les machines virtuelles VMware](tutorial-assess-vmware.md) pour la migration.
- [Évaluez les machines virtuelles Hyper-V](tutorial-assess-hyper-v.md) pour la migration.

