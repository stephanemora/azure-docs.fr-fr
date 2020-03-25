---
title: Migrer des machines locales à l’aide d’Azure Site Recovery
description: Cet article explique comment migrer des machines sur site vers Azure à l’aide d’Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 24015810a295ef88b7d3e63bfc464ddddef6b55f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73939635"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrer des machines sur site vers Azure


Cet article explique comment migrer des machines locales vers Azure à l’aide d’[Azure Site Recovery](site-recovery-overview.md). En règle générale, Site Recovery permet de gérer et d’orchestrer la reprise d’activité des machines locales et des machines virtuelles Azure. Toutefois, vous pouvez également l’utiliser à des fins de migration. La migration utilise les mêmes étapes que la reprise d’activité, à une exception près. Dans une migration, la dernière étape consiste à effectuer le basculement des machines de votre site local. Contrairement à la reprise d’activité, vous ne pouvez pas effectuer de restauration automatique sur un site local dans un scénario de migration.


Ce didacticiel vous montre comment migrer des machines virtuelles locales et des serveurs physiques vers Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer l’environnement source et cible pour la migration
> * Configurer une stratégie de réplication
> * Activer la réplication
> * Exécutez un test de migration afin de vérifier que tout fonctionne bien.
> * Exécuter un basculement unique vers Azure


> [!TIP]
> Vous pouvez désormais migrer des serveurs locaux vers Azure à l’aide du service Azure Migrate. [En savoir plus](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Avant de commencer

Notez que les appareils exportés par les pilotes paravirtualized ne sont pas pris en charge.


## <a name="prepare-azure-and-on-premises"></a>Préparer Azure et les serveurs locaux

1. Préparez Azure comme indiqué dans [cet article](tutorial-prepare-azure.md). Bien que cet article décrive les étapes de préparation de la reprise d’activité, ces étapes sont également valides pour la migration.
2. Préparez les serveurs [VMware](vmware-azure-tutorial-prepare-on-premises.md) locaux ou les serveurs [Hyper-V](hyper-v-prepare-on-premises-tutorial.md). Si vous migrez des machines physiques, vous n’avez rien à préparer. Vérifiez simplement la [matrice de prise en charge](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Sélectionner un objectif de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.
1. Cliquez sur **Coffres Recovery Services** > coffre.
2. Dans le menu Ressource, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
3. Dans **Objectif de protection**, sélectionnez les composants à migrer.
    - **VMware** : sélectionnez **Vers Azure** > **Oui, avec hyperviseur vSphere VMWare**.
    - **Machine physique** : sélectionnez **Vers Azure** > **Non virtualisé / Autre**.
    - **Hyper-V** : sélectionnez **Vers Azure** > **Oui, avec Hyper-V**. Si des machines virtuelles Hyper-V sont gérées par VMM, sélectionnez **Oui**.


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

**Scénario** | **Détails**
--- | --- 
VMware | Configurez l’[environnement source](vmware-azure-set-up-source.md), puis le [serveur de configuration](vmware-azure-deploy-configuration-server.md).
Machine physique | [Configurez](physical-azure-set-up-source.md) l’environnement source et le serveur de configuration.
Hyper-V | Configurer l’[environnement source](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Configurez l’[environnement source](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) pour Hyper-V déployé avec System Center VMM.

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez le modèle de déploiement Resource Manager.
3. Site Recovery vérifie les ressources Azure.
    - Si vous migrez des machines virtuelles VMware ou des serveurs physiques, Site Recovery vérifie que vous disposez d’un réseau Azure sur lequel les machines virtuelles Azure seront placées au moment de leur création après le basculement.
    - Si vous migrez des machines virtuelles Hyper-V, Site Recovery vérifie que vous disposez d’un compte de stockage Azure et d’un réseau compatibles.
4. Si vous migrez des machines virtuelles Hyper-V gérées par System Center VMM, configurez le [mappage réseau](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

**Scénario** | **Détails**
--- | --- 
VMware | Configurez une [stratégie de réplication](vmware-azure-set-up-replication.md) pour les machines virtuelles VMware.
Machine physique | Configurez une [stratégie de réplication](physical-azure-disaster-recovery.md#create-a-replication-policy) pour les machines physiques.
Hyper-V | Configurer une [stratégie de réplication](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Configurez une [stratégie de réplication](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) pour Hyper-V déployé avec System Center VMM.

## <a name="enable-replication"></a>Activer la réplication

**Scénario** | **Détails**
--- | --- 
VMware | [Activez la réplication](vmware-azure-enable-replication.md) pour les machines virtuelles VMware.
Machine physique | [Activez la réplication](physical-azure-disaster-recovery.md#enable-replication) pour les machines physiques.
Hyper-V | [Activer la réplication](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Activez la réplication](hyper-v-vmm-azure-tutorial.md#enable-replication) pour Hyper-V déployé avec System Center VMM.


## <a name="run-a-test-migration"></a>Exécuter un test de migration

Exécutez un [test de basculement](tutorial-dr-drill-azure.md) vers Azure afin de vérifier que tout fonctionne bien.


## <a name="migrate-to-azure"></a>Migrer vers Azure

Exécutez un basculement pour les machines que vous souhaitez migrer.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine > **Basculement**.
2. Dans **Basculer**, sélectionnez un **point de récupération** vers lequel basculer. Sélectionnez le dernier point de récupération.
3. Le paramètre de clé de chiffrement ne s’applique pas à ce scénario.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tente d’arrêter les machines virtuelles avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
5. Vérifiez que la machine virtuelle Azure s’affiche dans Azure comme prévu.
6. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Terminer la migration**. Cette opération effectue les actions suivantes :

   - Termine le processus de migration, arrête la réplication pour la machine virtuelle locale et arrête la facturation Site Recovery pour la machine virtuelle.
   - Cette étape nettoie les données de réplication. Elle ne supprime pas les machines virtuelles migrées.

     ![Terminer la migration](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **N’annulez pas un basculement en cours** : la réplication de la machine virtuelle est arrêtée avant que le basculement démarre. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous constaterez peut-être des délais de basculement plus longs pour les serveurs physiques, les machines virtuelles VMware Linux, les machines virtuelles VMware pour lesquelles le service DHCP n’est pas activé, et les machines virtuelles VMware qui ne disposent pas des pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Après la migration

Une fois que les machines sont migrées vers Azure, vous devez effectuer un certain nombre d’étapes.

Certaines étapes peuvent être automatisées dans le cadre du processus de migration avec la fonctionnalité de scripts d’automatisation intégrée dans des [plans de récupération](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Étapes de post-migration dans Azure

- Effectuez les éventuels ajustements post-migration de l’application, comme la mise à jour des chaînes de connexion de base de données et les configurations du serveur web. 
- Effectuez les tests finaux de réception de l’application et de la migration sur l’application migrée qui s’exécute maintenant dans Azure.
- [L’agent de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) gère les interactions entre une machine virtuelle Azure et le contrôleur Azure Fabric. Il est nécessaire pour certains services Azure, comme Sauvegarde Azure, Site Recovery et Azure Security.
    - Si vous migrez des machines VMware et des serveurs physiques, le programme d’installation du service Mobilité installe l’agent de machine virtuelle Azure disponible sur les machines Windows. Sur les machines virtuelles Linux, nous vous recommandons d’installer l’agent après le basculement.
    - Si vous migrez des machines virtuelles Azure vers une région secondaire, l’agent de machine virtuelle Azure doit être provisionné sur la machine virtuelle avant la migration.
    - Si vous migrez des machines virtuelles Hyper-V vers Azure, installez l’agent de machine virtuelle Azure sur la machine virtuelle Azure après la migration.
- Supprimez manuellement de la machine virtuelle les éventuels fournisseurs/agents Site Recovery. Si vous migrez des machines virtuelles VMware ou des serveurs physiques, désinstallez le service Mobilité de la machine virtuelle.
- Pour une meilleure résilience :
    - Sécurisez les données en sauvegardant les machines virtuelles Azure avec le service Sauvegarde Azure. [En savoir plus]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Conservez les charges de travail en cours d’exécution et disponibles en continu en répliquant des machines virtuelles Azure vers une région secondaire avec Site Recovery. [En savoir plus](azure-to-azure-quickstart.md).
- Pour renforcer la sécurité :
    - Verrouillez et limitez l’accès du trafic entrant avec [l’administration juste-à-temps]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) d’Azure Security Center.
    - Limitez le trafic réseau vers les points de terminaison de gestion avec des [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Déployez [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) pour sécuriser les disques, et protégez les données contre le vol et les accès non autorisés.
    - Découvrez plus d’informations sur la [sécurisation des ressources IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) et visitez [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- Pour la surveillance et la gestion :
    - Envisagez de déployer [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) pour surveiller l’utilisation et les coûts des ressources.

### <a name="post-migration-steps-on-premises"></a>Étapes de post-migration locales

- Déplacez le trafic de l’application vers l’application en cours d’exécution sur l’instance de machine virtuelle Azure migrée.
- Supprimez les machines virtuelles locales de votre inventaire des machines virtuelles locales.
- Supprimez les machines virtuelles locales des sauvegardes locales.
- Mettez à jour la documentation interne en y mentionnant le nouvel emplacement et la nouvelle adresse IP des machines virtuelles Azure.




## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez migré des machines virtuelles locales vers des machines virtuelles Azure. maintenant

> [!div class="nextstepaction"]
> [Configurez la reprise d’activité](azure-to-azure-replicate-after-migration.md) sur une région Azure secondaire pour les machines virtuelles Azure.

  
