---
title: Sélectionner une option de migration VMware avec la migration de serveur Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble des options de migration de machines virtuelles VMware vers Azure avec la migration de serveur Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 68026af5b96728ea66fd9e584e67e5e596e690fb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974663"
---
# <a name="select-a-vmware-migration-option"></a>Sélectionnez une option de migration VMware

Vous pouvez migrer des machines virtuelles VMware vers Azure à l’aide de l’outil de migration de serveur Azure Migrate. Cet outil offre deux options pour la migration de machines virtuelles VMware :

- Migration à l’aide de la réplication sans agent. Migrez des machines virtuelles sans avoir à installer quoi que ce soit.
- Migration avec un agent pour la réplication. Installez un agent sur la machine virtuelle pour la réplication.




## <a name="compare-migration-methods"></a>Comparer les méthodes de migration

Utilisez ces comparaisons sélectionnées pour vous aider à choisir la méthode à utiliser. Vous pouvez également consulter les spécifications de prise en charge complète pour la migration [sans agent](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) et [par agent](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements).

**Paramètre** | **Sans agent** | **Basé sur un agent**
--- | --- | ---
**Autorisations Azure** | Vous avez besoin d’autorisations pour créer un projet Azure Migrate et pour inscrire des applications Azure AD créées lorsque vous déployez l’appliance Azure Migrate. | Vous avez besoin des autorisations de Collaborateur sur l’abonnement Azure. 
**Réplication simultanée** | Un maximum de 100 machines virtuelles peuvent être répliquées simultanément à partir d’un vCenter Server.<br/> Si vous avez plus de 50 machines virtuelles pour la migration, créez plusieurs lots de machines virtuelles.<br/> La réplication multiple en une seule fois aura un impact sur les performances. | N/D
**Étapes de déploiement d’appliance** | L'[appliance Azure Migrate](migrate-appliance.md) est déployée en local. | L'[appliance de réplication Azure Migrate](migrate-replication-appliance.md) est déployée en local.
**Compatibilité Site Recovery** | Compatible. | Vous ne pouvez pas répliquer avec Azure Migrate Server Migration si vous avez défini la réplication pour une machine utilisant Site Recovery.
**Disque cible** | Disques managés | Disques managés
**Limites du disque** | Disque de système d’exploitation : 2 To<br/><br/> Disque de données : 4 To<br/><br/> Nombre maximal de disques : 60 | Disque de système d’exploitation : 2 To<br/><br/> Disque de données : 8 To<br/><br/> Nombre maximal de disques : 63
**Disques directs** | Non pris en charge | Pris en charge
**Démarrage UEFI** | Non pris en charge | La machine virtuelle migrée dans Azure est automatiquement convertie en machine virtuelle de démarrage du BIOS.<br/><br/> Le disque du système d’exploitation doit avoir jusqu’à quatre partitions et les volumes doivent être formatés avec NTFS.


## <a name="deployment-steps-comparison"></a>Comparaison des étapes de déploiement

Après avoir examiné les limitations, la compréhension des étapes impliquées dans le déploiement de chaque solution peut vous aider à choisir l’option appropriée.

**Tâche** | **Détails** |**Sans agent** | **Basé sur un agent**
--- | --- | --- | ---
**Évaluation** | Évaluez les ressources avant la migration.  L’évaluation est facultative. Nous vous suggérons d’évaluer les ordinateurs avant de les migrer, mais ce n’est pas une obligation. <br/><br/> À des fins d’évaluation, Azure Migrate configure une appliance légère pour découvrir et évaluer les machines virtuelles. | Si vous exécutez une migration sans agent après l’évaluation, la même appliance Azure Migrate qui a été configurée pour l’évaluation est utilisée pour la migration.  |  Si vous exécutez une migration basée sur un agent après l’évaluation, l’appliance configurée pour l’évaluation n’est pas utilisée pour la migration. Vous pouvez conserver l’appliance en place ou la supprimer si vous ne souhaitez pas procéder ultérieurement à une détection et à une évaluation.
**Préparez les machines virtuelles VMware pour la migration** | Configurez un certain nombre de paramètres sur les serveurs et les machines virtuelles VMware. | Obligatoire | Obligatoire
**Ajouter l’outil de migration de serveur** | Ajoutez l’outil de migration de serveur Azure Migrate dans le projet Azure Migrate. | Obligatoire | Obligatoire
**Déployez l’appliance Azure Migrate** | Configurez une appliance légère sur une machine virtuelle VMware pour la découverte et l’évaluation des machines virtuelles. | Obligatoire | Non requis.
**Installez le service Mobilité sur des machines virtuelles** | Installez le service Mbilité sur chaque machine virtuelle que vous souhaitez répliquer | Non requis | Obligatoire
**Déployer l’appliance de réplication de migration de serveur Azure Migrate** | Configurez une appliance sur une machine virtuelle VMware pour découvrir des machines virtuelles et établir un pont entre le service Mobilité exécuté sur les machines virtuelles et la migration du serveur Azure Migrate | Non requis | Obligatoire
**Répliquez les machines virtuelles**. Activez la réplication de machines virtuelles. | Configurez les paramètres de réplication et sélectionner les machines virtuelles à répliquer | Obligatoire | Obligatoire
**Exécutez un test de migration** | Exécutez un test de migration afin de vérifier que tout fonctionne bien. | Obligatoire | Obligatoire
**Exécutez une migration complète** | Migrez les machines virtuelles. | Obligatoire | Obligatoire




## <a name="next-steps"></a>Étapes suivantes

[Migrez des machines virtuelles VMware](tutorial-migrate-vmware.md) avec une migration sans agent.



