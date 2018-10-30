---
title: Basculer et restaurer automatiquement des machines virtuelles et des serveurs physiques VMware répliqués sur Azure avec Site Recovery | Microsoft Docs
description: Découvrez comment basculer des machines virtuelles et des serveurs physiques VMware vers Azure, et comment restaurer automatiquement sur le site local avec Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7e586e7e3ec8c16dcd215dbc11251d1b9fe928e1
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457039"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Basculer et restaurer automatiquement des machines virtuelles et des serveurs physiques VMware répliqués vers Azure

Ce didacticiel explique comment basculer une machine virtuelle VMware sur Azure. Une fois que vous avez procédé au basculement, vous restaurez automatiquement sur votre site local quand il est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifier la conformité des propriétés des machines virtuelles VMware aux spécifications d’Azure
> * Effectuer un basculement vers Azure
> * Créer un serveur de processus et un serveur cible maître pour la restauration automatique
> * Reprotéger des machines virtuelles Azure pour le site local
> * Basculer depuis Azure vers un site local
> * Reprotéger des machines virtuelles locales pour redémarrer la réplication vers Azure

>[!NOTE]
>Les tutoriels sont conçus pour vous montrer le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour en savoir plus sur les étapes de basculement de test, lisez le [guide de procédure](site-recovery-failover.md).

Il s’agit du cinquième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents.

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer des machines virtuelles VMware locales](vmware-azure-tutorial-prepare-on-premises.md)
3. [Configurer une récupération d’urgence](vmware-azure-tutorial.md)
4. [Effectuer un test de récupération d’urgence](tutorial-dr-drill-azure.md)
5. En plus des étapes suivantes, [examinez l’architecture](vmware-azure-architecture.md) du scénario de récupération d’urgence.

## <a name="failover-and-failback"></a>Basculement et restauration automatique

Le basculement et la restauration automatique comportent quatre étapes :

1. **Basculer vers Azure** : basculer les machines du site local vers Azure.
2. **Reprotéger des machines virtuelles Azure** : reprotégez les machines virtuelles Azure afin qu’elles soient répliquées sur les machines virtuelles VMware locales. La machine virtuelle locale est désactivée au cours de la reprotection. Cela permet de garantir la cohérence des données pendant la réplication.
3. **Basculer sur un site local** : effectuez un basculement pour restaurer automatiquement depuis Azure.
4. **Reprotéger les machines virtuelles locales** : une fois les données restaurées automatiquement, reprotégez les machines virtuelles locales vers lesquelles vous avez restauré automatiquement pour qu’elles soient répliquées vers Azure.

## <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Vérifiez les propriétés de la machine virtuelle et que la machine virtuelle est conforme aux [conditions requises pour Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** > Machine virtuelle.

2. Dans le volet **Élément répliqué**, vous voyez un récapitulatif des informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.

3. Dans **Calcul et réseau**, vous pouvez modifier le nom Azure, le groupe de ressources, la taille cible, le [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) et les [paramètres de disque managé](#managed-disk-considerations).

4. Vous pouvez afficher et modifier les paramètres réseau, notamment le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouvera après le basculement et l’adresse IP à lui affecter.

5. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques**.

## <a name="run-a-failover-to-azure"></a>Effectuer un basculement vers Azure

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculer**.

2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
   - **Dernier** : cette option traite d’abord toutes les données envoyées à Site Recovery. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle Azure créée après le basculement a toutes les données qui ont été répliquées vers Site Recovery quand le basculement a été déclenché.
   - **Dernier point traité** : cette option bascule la machine virtuelle vers le dernier point de récupération traité par Site Recovery. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.
   - **Dernier point de cohérence des applications** : cette option bascule la machine virtuelle vers le dernier point de récupération de cohérence des applications traité par Site Recovery.
   - **Personnalisé** : spécifiez un point de récupération.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** pour tenter d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous pouvez remarquer que **les temps de basculement de test sont plus longs** pour des machines virtuelles VMware qui utilisent un service de mobilité de version antérieure à 9.8, des serveurs physiques, des machines virtuelles VMware Linux, des machines virtuelles Hyper-V protégées en tant que serveurs physiques. Les machines virtuelles VMware pour lesquels le service DHCP n’est pas activé, et les machines virtuelles VMware qui n’ont pas les pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **N’annulez pas un basculement en cours** : avant que le basculement soit démarré, la réplication de la machine virtuelle est arrêtée.
> Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Se connecter à une machine virtuelle ayant basculé dans Azure

1. Si vous souhaitez vous connecter à des machines virtuelles Azure à l’aide de RDP/SSH après le basculement, respectez les exigences récapitulées dans le tableau [ici](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Après le basculement, accédez à la machine virtuelle et validez-la en vous [connectant](../virtual-machines/windows/connect-logon.md) à cette machine.
3. Après la validation, cliquez sur **Valider** pour finaliser le point de récupération de la machine virtuelle une fois le basculement effectué. Tous les autres points de récupération disponibles sont supprimés. Cette étape termine l’activité de basculement.

>[!TIP]
> La fonction de **modification du point de récupération** vous aide à sélectionner un autre point de récupération après le basculement, si le comportement de la machine virtuelle ayant basculé ne vous satisfait pas. Après la **validation**, cette option n’est plus disponible.

Suivez les étapes décrites [ici](site-recovery-failover-to-azure-troubleshoot.md) pour résoudre les problèmes de connectivité après le basculement.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Préparation à la reprotection de la machine virtuelle Azure

- Vous pouvez utiliser le serveur de processus local (serveur de processus intégré) qui est installé automatiquement sur le serveur de configuration en tant que partie de la configuration **si vous disposez d’une connexion Microsoft Azure ExpressRoute**.

> [!IMPORTANT]
> Si vous disposez d’une connexion VPN entre votre environnement local et Azure, vous devez configurer une machine virtuelle Azure en tant que serveur de processus pour la reprotection et la restauration automatique. Pour configurer un serveur de processus dans Azure, suivez les instructions de [cet article](vmware-azure-set-up-process-server-azure.md).

Pour plus d’informations concernant les conditions préalables à la reprotection et la restauration automatique, reportez-vous à cette [section] (vmware-azure-reprotect.md##before-you-begin). 

### <a name="configure-the-master-target-server"></a>Configurer le serveur cible maître

Un serveur cible maître reçoit et gère les données de réplication pendant la restauration automatique à partir d’Azure. Par défaut, il est disponible sur le serveur de configuration local. Dans ce didacticiel, nous allons utiliser le serveur cible maître par défaut.

>[!NOTE]
>Pour protéger une machine virtuelle basée sur Linux, vous devez créer un serveur cible maître distinct. [Cliquez ici](vmware-azure-install-linux-master-target.md) pour en savoir plus.

Si la machine virtuelle est sur un **hôte VMware ESXi qui est géré par un serveur vCenter**, le serveur cible maître doit avoir accès au magasin de données de la machine virtuelle (VMDK) pour écrire les données répliquées sur les disques de machine virtuelle. Vérifiez que le magasin de données de la machine virtuelle est monté sur l’hôte du serveur cible maître avec accès en lecture/écriture.

Si la machine virtuelle est sur un hôte **VMware ESXi qui n’est pas géré par un serveur vCenter**, le service Site Recovery crée une machine virtuelle lors de la reprotection. La machine virtuelle est créée sur l’hôte ESX où vous créez le serveur cible maître.
Le disque dur de la machine virtuelle doit être dans une banque de données accessible par l’hôte sur lequel le serveur cible maître s’exécute.

Si la machine virtuelle **n’utilise pas vCenter**, vous devez effectuer la détection de l’hôte sur lequel le serveur cible maître s’exécute avant de pouvoir reprotéger la machine. Ceci est vrai également pour la restauration automatique des serveurs physiques. Une autre option, si la machine virtuelle locale existe, consiste à la supprimer avant de procéder à une restauration automatique. L’opération de restauration automatique crée ensuite une machine virtuelle sur le même hôte que l’ordinateur hôte ESX cible maître. Si vous effectuez la restauration automatique vers un autre emplacement, les données sont récupérées dans la même banque de données et sur le même ordinateur hôte ESX que ceux qui sont utilisés par le serveur cible maître local.

Vous ne pouvez pas utiliser Storage vMotion sur le serveur cible maître. Si vous le faites, la restauration automatique ne fonctionne pas, car les disques ne sont pas disponibles pour cela. Excluez les serveurs cibles maîtres de votre liste vMotion.

>[!Warning]
>Si vous utilisez un autre serveur cible maître pour reprotéger un groupe de réplication, le serveur ne peut fournir aucun point dans le temps commun.

## <a name="reprotect-azure-vms"></a>Reprotéger les machines virtuelles Azure

Le fait de reprotéger la machine virtuelle Azure entraîne la réplication des données à la machine virtuelle locale. C’est une étape obligatoire avant le basculement depuis Azure vers la machine virtuelle locale. Suivez les instructions ci-dessous pour effectuer la reprotection.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez avec le bouton droit de la souris sur la machine virtuelle qui a été basculée > **Reprotéger**.
2. Dans **Reprotéger**, vérifiez que **D’Azure à local** est sélectionné.
3. Spécifiez le serveur cible maître local et le serveur de processus.
4. Dans **Magasin de données**, sélectionnez le magasin de données où vous voulez récupérer les disques locaux. Si la machine virtuelle a été supprimée, les disques sont créés sur cette banque de données. Ce paramètre est ignoré si les disques existent déjà, mais vous devez spécifier une valeur.
5. Sélectionnez le lecteur de conservation du serveur cible maître. La stratégie de restauration automatique est sélectionnée automatiquement.
6. Cliquez sur **OK** pour commencer l’opération de reprotection. Un travail est lancé pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Tâches** .
7. Lorsque l’état de la machine virtuelle sur les **éléments répliqués** est **Protégé**, cela signifie que la machine est prête pour le basculement au niveau local.

> [!NOTE]
> La machine virtuelle Azure peut être récupérée sur une machine virtuelle locale existante, ou à un autre emplacement. Pour en savoir plus, lisez [cet article](concepts-types-of-failback.md).

## <a name="run-a-failover-from-azure-to-on-premises"></a>Effectuer un basculement depuis Azure vers le site local

Pour effectuer une réplication en retour vers votre site local, une stratégie de restauration automatique est utilisée. Cette stratégie a été créée automatiquement quand vous avez créé une stratégie de réplication pour la réplication vers Azure :

- La stratégie est automatiquement associée au serveur de configuration.
- La stratégie ne peut pas être modifiée.
- Les valeurs de la stratégie sont :
    - Seuil d’objectif de point de récupération : 15 minutes
    - Rétention de point de récupération : 24 heures
    - Fréquence des captures instantanées de cohérence d’application : 60 minutes

Effectuez le basculement en procédant comme suit :

1. Dans la page **Éléments répliqués**, cliquez avec le bouton droit de la souris sur la machine > **Basculement**.
2. Dans **Confirmer le basculement**, vérifiez que le sens du basculement est depuis Azure.
    ![sens-du-basculement](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Sélectionnez le point de récupération à utiliser pour le basculement. Un point de récupération de cohérence au niveau application se produit avant le dernier point dans le temps et entraîne une perte de données.

    >[!WARNING]
    >Quand le basculement est effectué, Site Recovery arrête les machines virtuelles Azure et démarre la machine virtuelle locale. Il faut prévoir un temps d’indisponibilité : choisissez donc un moment approprié.

4. La progression du travail peut faire l’objet d’un suivi dans **Coffre Recovery Services** > **Surveillance et rapports** > **Travaux Site Recovery**.
5. Après l’achèvement du basculement, cliquez avec le bouton droit de la souris sur la machine virtuelle, puis cliquez sur **Valider**. Ceci déclenche une tâche qui supprime les machines virtuelles Azure.
6. Vérifiez que les machines virtuelles Azure ont été arrêtées comme attendu.

## <a name="reprotect-on-premises-machines-to-azure"></a>Reprotéger les machines locales sur Azure

Les données doivent maintenant être de retour sur votre site local, mais elles ne sont pas répliquées vers Azure. Vous pouvez redémarrer la réplication vers Azure comme suit :

1. Dans le coffre, sous **Éléments protégés** >**Éléments répliqués**, sélectionnez les machines virtuelles qui ont été restaurées automatiquement, puis cliquez sur **Reprotéger**.
2. Sélectionnez le serveur de processus utilisé pour envoyer les données répliquées vers Azure, puis cliquez sur **OK**.

Une fois que la reprotection est terminée, la machine virtuelle est répliquée vers Azure et vous pouvez effectuer un basculement si nécessaire.
