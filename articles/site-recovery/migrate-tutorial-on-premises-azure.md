---
title: Migrer des machines sur site vers Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment migrer des machines sur site vers Azure à l’aide d’Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/28/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0134f6c83548ae5ffb4924ecf7d652ce89910340
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210616"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrer des machines sur site vers Azure

Outre l’utilisation du service [Azure Site Recovery](site-recovery-overview.md) pour gérer et orchestrer la récupération d’urgence des ordinateurs locaux et des machines virtuelles Azure dans le cadre de la continuité d’activité et de la récupération d’urgence, vous pouvez utiliser Site Recovery pour gérer la migration des machines locales sur Azure.


Ce didacticiel vous montre comment migrer des machines virtuelles locales et des serveurs physiques vers Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Sélectionner un objectif de réplication
> * Configurer l’environnement cible et source
> * Configurer une stratégie de réplication
> * Activer la réplication
> * Exécutez un test de migration afin de vérifier que tout fonctionne bien.
> * Exécuter un basculement unique vers Azure

Il s’agit du troisième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. Préparez les serveurs [VMware](vmware-azure-tutorial-prepare-on-premises.md) locaux ou les serveurs [Hyper-V](hyper-v-prepare-on-premises-tutorial.md).

Avant de commencer, il est utile d’examiner les architectures [VMware](vmware-azure-architecture.md) et [Hyper-V](hyper-v-azure-architecture.md) pour la récupération d’urgence.


## <a name="prerequisites"></a>Prérequis

Les appareils exportés par les pilotes paravirtualisés ne sont pas pris en charge.


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

1. Connectez-vous au [portail Azure](https://portal.azure.com) > **Recovery Services**.
2. Cliquez sur **Créer une ressource** > **Surveillance + Gestion** > **Backup and Site Recovery**.
3. Dans **Nom**, indiquez le nom convivial **ContosoVMVault**. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
4. Créez un groupe de ressources **ContosoRG**.
5. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pour accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

   ![Nouveau coffre](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

Le nouveau coffre est ajouté à la zone **Tableau de bord** dans **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.



## <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.
1. Cliquez sur **Coffres Recovery Services** > coffre.
2. Dans le menu Ressource, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
3. Dans **Objectif de protection**, sélectionnez les composants à migrer.
    - **VMware** : sélectionnez **Vers Azure** > **Oui, avec hyperviseur vSphere VMWare**.
    - **Machine physique** : sélectionnez **Vers Azure** > **Non virtualisé / Autre**.
    - **Hyper-V** : sélectionnez **Vers Azure** > **Oui, avec Hyper-V**. Si des machines virtuelles Hyper-V sont gérées par VMM, sélectionnez **Oui**.


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

- [Configurez](vmware-azure-tutorial.md#set-up-the-source-environment) l’environnement source pour les machines virtuelles VMware.
- [Configurez](physical-azure-disaster-recovery.md#set-up-the-source-environment) l’environnement source pour les serveurs physiques.
- [Configurez](hyper-v-azure-tutorial.md#set-up-the-source-environment) l’environnement source pour les machines virtuelles Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez le modèle de déploiement Resource Manager.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

- [Configurez une stratégie de réplication](vmware-azure-tutorial.md#create-a-replication-policy) pour les machines virtuelles VMware.
- [Configurez une stratégie de réplication](physical-azure-disaster-recovery.md#create-a-replication-policy) pour les serveurs physiques.
- [Configurez une stratégie de réplication](hyper-v-azure-tutorial.md#set-up-a-replication-policy) pour les machines virtuelles Hyper-V.


## <a name="enable-replication"></a>Activer la réplication

- [Activez la réplication](vmware-azure-tutorial.md#enable-replication) pour les machines virtuelles VMware.
- [Activez la réplication](physical-azure-disaster-recovery.md#enable-replication) des serveurs physiques.
- Activez la réplication des machines virtuelles Hyper-V [avec](hyper-v-vmm-azure-tutorial.md#enable-replication) ou [sans VMM](hyper-v-azure-tutorial.md#enable-replication).


## <a name="run-a-test-migration"></a>Exécuter un test de migration

Exécutez un [test de basculement](tutorial-dr-drill-azure.md) vers Azure afin de vérifier que tout fonctionne bien.


## <a name="migrate-to-azure"></a>Migrer vers Azure

Exécutez un basculement pour les machines que vous souhaitez migrer.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine > **Basculement**.
2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Sélectionnez le point de récupération le plus récent.
3. Le paramètre de clé de chiffrement ne s’applique pas à ce scénario.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tente d’arrêter les machines virtuelles avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
5. Vérifiez que la machine virtuelle Azure s’affiche dans Azure comme prévu.
6. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Terminer la migration**. Cette opération termine le processus de migration, interrompt la réplication pour la machine virtuelle et arrête la facturation Site Recovery pour la machine virtuelle.

    ![Terminer la migration](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **N’annulez pas un basculement en cours** : la réplication de la machine virtuelle est arrêtée avant que le basculement démarre. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous constaterez peut-être des délais de basculement plus longs pour les serveurs physiques, les machines virtuelles VMware Linux, les machines virtuelles VMware pour lesquelles le service DHCP n’est pas activé, et les machines virtuelles VMware qui ne disposent pas des pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Après la migration

Une fois que les machines sont migrées vers Azure, vous devez effectuer un certain nombre d’étapes.

Certaines étapes peuvent être automatisées dans le cadre du processus de migration avec la fonctionnalité de scripts d’automatisation intégrée dans des [plans de récupération]( https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)   


### <a name="post-migration-steps-in-azure"></a>Étapes de post-migration dans Azure

- Effectuez les éventuels ajustements post-migration de l’application, comme la mise à jour des chaînes de connexion de base de données et les configurations du serveur web. 
- Effectuez les tests finaux de réception de l’application et de la migration sur l’application migrée qui s’exécute maintenant dans Azure.
- [L’agent de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) gère les interactions entre une machine virtuelle Azure et le contrôleur Azure Fabric. Il est nécessaire pour certains services Azure, comme Sauvegarde Azure, Site Recovery et Azure Security.
    - Si vous migrez des machines VMware et des serveurs physiques, le programme d’installation du service Mobilité installe l’agent de machine virtuelle Azure disponible sur les machines Windows. Sur les machines virtuelles Linux, nous vous recommandons d’installer l’agent après le basculement. a
    - Si vous migrez des machines virtuelles Azure vers une région secondaire, l’agent de machine virtuelle Azure doit être provisionné sur la machine virtuelle avant la migration.
    - Si vous migrez des machines virtuelles Hyper-V vers Azure, installez l’agent de machine virtuelle Azure sur la machine virtuelle Azure après la migration.
- Supprimez manuellement de la machine virtuelle les éventuels fournisseurs/agents Site Recovery. Si vous migrez des machines virtuelles VMware ou des serveurs physiques, [désinstallez le service Mobilité][vmware-azure-install-mobility-service.md#uninstall-mobility-service-on-a-windows-server-computer] de la machine virtuelle Azure.
- Pour une meilleure résilience :
    - Sécurisez les données en sauvegardant les machines virtuelles Azure avec le service Sauvegarde Azure. [Plus d’informations]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal)
    - Conservez les charges de travail en cours d’exécution et disponibles en continu en répliquant des machines virtuelles Azure vers une région secondaire avec Site Recovery. [Plus d’informations](azure-to-azure-quickstart.md)
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

Dans ce didacticiel, vous avez migré des machines virtuelles locales vers des machines virtuelles Azure. Vous pouvez maintenant [configurer la reprise d’activité](azure-to-azure-replicate-after-migration.md) sur une région Azure secondaire pour les machines virtuelles Azure.

  
