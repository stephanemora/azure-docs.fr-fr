---
title: Sélectionner une option de migration VMware avec la migration de serveur Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble des options de migration de machines virtuelles VMware vers Azure avec la migration de serveur Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810148"
---
# <a name="select-a-vmware-migration-option"></a>Sélectionnez une option de migration VMware

Vous pouvez migrer des machines virtuelles VMware vers Azure à l’aide de l’outil de migration de serveur Azure Migrate. Cet outil offre deux options pour la migration de machines virtuelles VMware :

- Migration à l’aide de la réplication sans agent. Migrez des machines virtuelles sans avoir à installer quoi que ce soit.
- Migration avec un agent pour la réplication. Installez un agent sur la machine virtuelle pour la réplication.


Bien que la réplication sans agent soit plus facile du point de vue du déploiement, elle présente actuellement un certain nombre de limitations.

## <a name="agentless-migration-limitations"></a>Limitations de migration sans agent

Les limitations sont les suivantes :

- **Réplication simultanée** : Un maximum de 50 machines virtuelles peuvent être répliquées simultanément à partir d’un vCenter Server.<br/> Si vous avez plus de 50 machines virtuelles pour la migration, créez plusieurs lots de machines virtuelles.<br/> La réplication multiple en une seule fois aura un impact sur les performances.
- **Disques de machine virtuelle** : Une machine virtuelle que vous souhaitez migrer doit avoir 60 disques ou moins.
- **Systèmes d’exploitation de machine virtuelle** : En général, Azure Migrate peut migrer n’importe quel système d’exploitation Windows Server ou Linux, mais cela peut nécessiter des modifications sur les machines virtuelles afin qu’elles puissent s’exécuter dans Azure. Azure Migrate effectue automatiquement les modifications pour les systèmes d’exploitation suivants :
    - Red Hat Enterprise Linux 6.5+, 7.0+
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - Pour les autres systèmes d’exploitation, vous devez effectuer les ajustements manuellement avant la migration. Le [didacticiel sur la migration](tutorial-migrate-vmware.md) explique comment procéder.
- **Démarrage Linux** : Si /boot se trouve sur une partition dédiée, il doit résider sur le disque du système d’exploitation et ne pas être réparti sur plusieurs disques.<br/> Si /boot fait partie de la partition racine (/), la partition « / » doit se trouver sur le disque du système d’exploitation et ne pas couvrir d’autres disques.
- **Démarrage UEFI** : Les machines virtuelles avec démarrage UEFI ne sont pas prises en charge pour la migration.
- **Disques/volumes chiffrés (BitLocker, cryptfs)**  : Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration.
- **RDM/disques directs** : Si les machines virtuelles ont des disques RDM ou directs, ces disques ne seront pas répliqués dans Azure
- **NFS** : Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués.
- **Stockage cible** : Vous pouvez uniquement migrer des machines virtuelles VMware vers des machines virtuelles Azure avec des disques managés (HDD Standard, SSD Premium).



## <a name="deployment-steps-comparison"></a>Comparaison des étapes de déploiement

Après avoir examiné les limitations, la compréhension des étapes impliquées dans le déploiement de chaque solution peut vous aider à choisir l’option appropriée.

**Tâche** | **Détails** |**Sans agent** | **Basé sur un agent**
--- | --- | --- | ---
**Préparez les machines virtuelles VMware pour la migration** | Configurez un certain nombre de paramètres sur les serveurs et les machines virtuelles VMware. | Obligatoire | Obligatoire
**Ajouter l’outil de migration de serveur** | Ajoutez l’outil de migration de serveur Azure Migrate dans le projet Azure Migrate. | Obligatoire | Obligatoire
**Déployez l’appliance Azure Migrate** | Configurez une appliance légère sur une machine virtuelle VMware pour la découverte et l’évaluation des machines virtuelles. | Obligatoire | Non requis.
**Installez le service Mobilité sur des machines virtuelles** | Installez le service Mbilité sur chaque machine virtuelle que vous souhaitez répliquer | Non requis | Obligatoire
**Déployer l’appliance de réplication de migration de serveur Azure Migrate** | Configurez une appliance sur une machine virtuelle VMware pour découvrir des machines virtuelles et établir un pont entre le service Mobilité exécuté sur les machines virtuelles et la migration du serveur Azure Migrate | Non requis | Obligatoire
**Répliquez les machines virtuelles**. Activez la réplication de machines virtuelles. | Configurez les paramètres de réplication et sélectionner les machines virtuelles à répliquer | Obligatoire | Obligatoire
**Exécutez un test de migration** | Exécutez un test de migration afin de vérifier que tout fonctionne bien. | Obligatoire | Obligatoire
**Exécutez une migration complète** | Migrez les machines virtuelles. | Obligatoire | Obligatoire




## <a name="next-steps"></a>Étapes suivantes

[Migrez des machines virtuelles VMware](tutorial-migrate-vmware.md) à l’aide d’une migration sans agent.



