---
title: Basculer et restaurer automatiquement des machines virtuelles et des serveurs physiques VMware lors de la récupération d’urgence sur Azure avec Site Recovery | Microsoft Docs
description: Découvrez comment basculer des machines virtuelles et des serveurs physiques VMware vers Azure, et comment restaurer automatiquement sur le site local lors de la récupération d’urgence avec Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797663"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Effectuer un basculement et une restauration automatique des machines virtuelles VMware

Cet article explique comment effectuer le basculement d’une machine virtuelle VMware locale vers le service Azure [Azure Site Recovery](site-recovery-overview.md). 

Il s’agit du cinquième tutoriel d’une série qui montre comment configurer la reprise d’activité sur Azure pour des machines locales.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifier la conformité des propriétés des machines virtuelles VMware aux spécifications d’Azure
> * Effectuer un basculement vers Azure


> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour en savoir plus sur le basculement, [consultez cet article](site-recovery-failover.md).

## <a name="before-you-start"></a>Avant de commencer
Effectuez les tutoriels précédents :

1. Vérifiez que vous avez [configuré Azure](tutorial-prepare-azure.md) pour permettre la reprise d’activité locale des machines virtuelles VMware, des machines virtuelles Hyper-V et des machines physiques sur Azure.
2. Préparez votre environnement [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) local à la reprise d’activité. Si vous configurez la reprise d’activité pour les serveurs physiques, consultez la [matrice de prise en charge](vmware-physical-secondary-support-matrix.md).
3. Configurez la reprise d’activité pour les [machines virtuelles VMware](vmware-azure-tutorial.md), les [machines virtuelles Hyper-V](hyper-v-azure-tutorial.md) ou les [machines physiques](physical-azure-disaster-recovery.md).
4. Exécutez une [simulation de reprise d’activité](tutorial-dr-drill-azure.md) pour vérifier que tout fonctionne comme prévu.


## <a name="failover-and-failback"></a>Basculement et restauration automatique

Le basculement et la restauration automatique comportent quatre étapes :

1. **Basculer vers Azure** : Quand votre site principal local tombe en panne, effectuez un basculement des machines sur Azure. Une fois le basculement effectué, les machines virtuelles Azure sont créées à partir des données répliquées.
2. **Reprotéger des machines virtuelles Azure** : Dans Azure, reprotégez les machines virtuelles Azure pour qu’elles puissent être répliquées sur des machines virtuelles VMware locales. La machine virtuelle locale est désactivée au cours de la reprotection, afin de garantir la cohérence des données.
3. **Basculer sur un site local** : Une fois que votre site local est opérationnel, exécutez une restauration automatique à partir d’Azure.
4. **Reprotéger les machines virtuelles locales** : Une fois les données restaurées automatiquement, reprotégez les machines virtuelles locales vers lesquelles vous avez effectué la restauration automatique, pour qu’elles puissent être répliquées sur Azure.

## <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Avant d’exécuter un basculement, vérifiez les propriétés des machines virtuelles. Vérifiez également que ces dernières sont conformes aux [exigences relatives à Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Vérifiez les propriétés suivantes :

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** > Machine virtuelle.

2. Dans le volet **Élément répliqué**, vous voyez un récapitulatif des informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.

3. Dans **Calcul et réseau**, vous pouvez modifier le nom Azure, le groupe de ressources, la taille cible, le [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) ainsi que les paramètres de disque managé

4. Vous pouvez afficher et modifier les paramètres réseau, notamment le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouvera après le basculement et l’adresse IP à lui affecter.

5. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques**.

## <a name="run-a-failover-to-azure"></a>Effectuer un basculement vers Azure

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculer**.
2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
   - **Les dernières** : cette option traite d’abord toutes les données envoyées à Site Recovery. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle Azure créée après le basculement a toutes les données qui ont été répliquées vers Site Recovery quand le basculement a été déclenché.
   - **Dernier point traité** : Cette option permet d’effectuer une restauration de la machine virtuelle au dernier point de récupération traité par Site Recovery. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.
   - **Dernier point de cohérence des applications** : cette option bascule la machine virtuelle vers le dernier point de récupération de cohérence des applications traité par Site Recovery.
   - **Personnalisé** : spécifiez un point de récupération.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** pour tenter d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous noterez éventuellement que les délais des tests de basculement sont plus longs pour les machines suivantes :
- Machines virtuelles VMware exécutant une version du service Mobilité antérieure à la version 9.8
- Serveurs physiques
- Machines virtuelles VMware Linux
- Machines virtuelles Hyper-V protégées en tant que serveurs physiques
- Machines virtuelles VMware sur lesquelles le service DHCP n’est pas activé
- Machines virtuelles VMware qui n’ont pas les pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **N’annulez pas un basculement en cours** : Avant le démarrage du basculement, la réplication de la machine virtuelle est arrêtée. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

## <a name="connect-to-failed-over-vm"></a>Se connecter à une machine virtuelle ayant fait l’objet d’un basculement

1. Si vous souhaitez vous connecter aux machines virtuelles Azure via RDP/SSH après un basculement, [vérifiez les exigences suivantes](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Une fois le basculement effectué, accédez à la machine virtuelle et validez-la en vous [connectant](../virtual-machines/windows/connect-logon.md) à celle-ci.
3. Utilisez **Changer le point de récupération**, si vous souhaitez vous servir d’un autre point de récupération après le basculement. Une fois le basculement validé au cours de l’étape suivante, cette option n’est plus disponible.
4. Une fois la validation effectuée, cliquez sur **Valider** pour finaliser le point de récupération de la machine virtuelle après le basculement.
5. Une fois la validation effectuée, tous les autres points de récupération disponibles sont supprimés. Ainsi, le basculement est effectué.

>[!TIP]
> Si vous rencontrez des problèmes de connectivité après le basculement, suivez ce [guide de résolution des problèmes](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois le basculement effectué, reprotégez les machines virtuelles Azure locales. Ensuite, une fois les machines virtuelles reprotégées et répliquées sur le site local, effectuez une restauration automatique à partir d’Azure quand vous êtes prêt.

> [!div class="nextstepaction"]
> [Reprotéger les machines virtuelles Azure](vmware-azure-reprotect.md)
> [Effectuer une restauration automatique à partir d’Azure](vmware-azure-failback.md) 
