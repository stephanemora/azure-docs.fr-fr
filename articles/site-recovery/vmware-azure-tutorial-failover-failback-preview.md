---
title: Effectuer le basculement de machines virtuelles VMware vers Azure à l’aide de Site Recovery - Préversion
description: Découvrez comment effectuer le basculement de machines virtuelles VMware vers Azure dans Azure Site Recovery - Préversion
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: 42ccd96287fefed0a6c7ef4cee8ddf776541c8ba
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447058"
---
# <a name="fail-over-vmware-vms---preview"></a>Effectuer le basculement de machines virtuelles VMware - Préversion

Cet article explique comment effectuer le basculement d’une machine virtuelle VMware locale vers Azure avec [Azure Site Recovery](site-recovery-overview.md) - Préversion.

Pour plus d’informations sur le basculement dans les versions Classic, consultez [cet article](vmware-azure-tutorial-failover-failback.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Vérifier que les propriétés des machines virtuelles VMware sont conformes aux spécifications d’Azure
> * Effectuer le basculement de certaines machines virtuelles vers Azure

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut quand cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour obtenir des informations détaillées sur le basculement, consultez [Basculer des machines virtuelles et des serveurs physiques](site-recovery-failover.md).

[Découvrez](failover-failback-overview.md#types-of-failover) les différents types de basculements. Si vous souhaitez effectuer le basculement de plusieurs machines virtuelles dans le cadre d’un plan de récupération, consultez [cet article](site-recovery-failover.md).

## <a name="before-you-start"></a>Avant de commencer

Effectuez les tutoriels précédents :

1. Vérifiez que vous avez [configuré Azure](tutorial-prepare-azure.md) pour permettre la récupération d’urgence locale de machines virtuelles VMware.
2. Préparez votre environnement [VMware](vmware-azure-tutorial-prepare-on-premises.md) local à la reprise d’activité.
3. Configurez la reprise d’activité pour les [machines virtuelles VMware](vmware-azure-set-up-replication-tutorial-preview.md).
4. Exécutez une [simulation de reprise d’activité](tutorial-dr-drill-azure.md) pour vérifier que tout fonctionne comme prévu.

## <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Avant d’exécuter un basculement, vérifiez les propriétés des machines virtuelles pour vous assurer que ces dernières satisfont aux [exigences relatives à Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Vérifiez les propriétés suivantes :

1. Dans **Éléments protégés**, sélectionnez **Éléments répliqués**, puis sélectionnez la machine virtuelle à vérifier.

2. Dans le volet **Élément répliqué**, vous voyez un récapitulatif des informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Sélectionnez **Propriétés** pour obtenir plus de détails.

3. Dans **Calcul et réseau**, vous pouvez modifier ces propriétés en fonction des besoins :
    * Nom Azure
    * Resource group
    * Taille de la cible
    * Paramètres des disques managés

4. Vous pouvez afficher et modifier les paramètres réseau, notamment :

    * Les réseau et sous-réseau sur lesquels se trouve la machine virtuelle Azure après le basculement
    * L’adresse IP à lui affecter

5. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques**.

## <a name="run-a-failover-to-azure"></a>Effectuer un basculement vers Azure

1. Dans **Paramètres** > **Éléments répliqués**, sélectionnez la machine virtuelle à basculer, puis **Basculement**.
2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
   * **Les dernières** : cette option traite d’abord toutes les données envoyées à Site Recovery. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle Azure créée après le basculement a toutes les données qui ont été répliquées vers Site Recovery quand le basculement a été déclenché.
   * **Dernier point traité** : Cette option permet d’effectuer une restauration de la machine virtuelle au dernier point de récupération traité par Site Recovery. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.
   * **Dernier point de cohérence des applications** : cette option bascule la machine virtuelle vers le dernier point de récupération de cohérence des applications traité par Site Recovery.
   * **Personnalisé** : cette option vous permet de spécifier un point de récupération.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** pour tenter d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement se poursuit même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.

  Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ 8 à 10 minutes. Vous noterez éventuellement que les délais des tests de basculement sont plus longs pour les machines suivantes :

  * Machines virtuelles VMware Linux
  * Machines virtuelles VMware sur lesquelles le service DHCP n’est pas activé
  * Machines virtuelles VMware qui n’ont pas les pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

  > [!WARNING]
  > N’annulez pas un basculement en cours. Avant le démarrage du basculement, la réplication de la machine virtuelle est arrêtée. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

## <a name="connect-to-failed-over-vm"></a>Se connecter à une machine virtuelle basculée

1. Si vous souhaitez vous connecter à des machines virtuelles Azure après le basculement en utilisant le protocole RDP (Remote Desktop Protocol) et Secure Shell (SSH), [vérifiez que les exigences ont été satisfaites](failover-failback-overview.md#connect-to-azure-after-failover).
2. Une fois le basculement effectué, accédez à la machine virtuelle et validez-la en vous [connectant](../virtual-machines/windows/connect-logon.md) à celle-ci.
3. Utilisez **Changer le point de récupération**, si vous souhaitez vous servir d’un autre point de récupération après le basculement. Une fois le basculement validé au cours de l’étape suivante, cette option n’est plus disponible.
4. Une fois la validation effectuée, sélectionnez **Valider** pour finaliser le point de récupération de la machine virtuelle après le basculement.
5. Une fois que vous avez effectué la validation, tous les autres points de récupération disponibles sont supprimés. Cette étape marque la fin du basculement.

>[!TIP]
> Si vous rencontrez des problèmes de connectivité après le basculement, suivez le [guide de résolution des problèmes](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="planned-failover-from-azure-to-on-premises"></a>Basculement planifié à partir d’Azure vers un emplacement local

Vous pouvez effectuer un basculement planifié à partir d’Azure vers un emplacement local. Puisqu’il s’agit d’une activité de basculement planifié, le point de récupération est généré après le déclenchement de la tâche de basculement planifié.

>[!NOTE]
> Avant de continuer, assurez-vous que l’intégrité de la réplication de la machine est saine. Assurez-vous également que l’appliance et tous ses composants sont sains.

Lorsque le basculement planifié est déclenché, les modifications en attente sont copiées localement, un dernier point de récupération de la machine virtuelle est généré et la machine virtuelle Azure est arrêtée. Après cela, la machine locale est activée.

Une fois le basculement planifié réussi, la machine est active dans votre environnement local.

> [!NOTE]
> Si une machine protégée possède des disques iSCSI, la configuration est conservée dans Azure lors du basculement. Après le basculement planifié à partir d’Azure vers un emplacement local, la configuration iSCSI ne peut pas être conservée. Par conséquent, les disques vmdk sont créés sur la machine locale. Pour supprimer les disques en double, supprimez le disque iSCSI, car les données sont remplacées par des disques vmdk.


### <a name="failed-over-vm-to-azure---requirements"></a>Basculement d’une machine virtuelle vers Azure - Configuration requise

Vérifiez les éléments suivants pour la machine virtuelle, une fois qu’elle est basculée vers Azure :

1. La machine virtuelle dans Azure doit toujours être activée.
2. Vérifiez que les services de l’agent de mobilité *service 1* et *service 2* sont en cours d’exécution sur la machine virtuelle. Cela permet de s’assurer que l’agent de mobilité de la machine virtuelle peut communiquer avec les services Azure Site Recovery dans Azure.
3. Les URL mentionnées [ici](vmware-azure-architecture-preview.md#set-up-outbound-network-connectivity) sont accessibles à partir de la machine virtuelle.

## <a name="cancel-planned-failover"></a>Annuler un basculement planifié

Si votre environnement local n’est pas prêt ou si vous rencontrez un problème, vous pouvez annuler le basculement planifié. Vous pouvez ensuite effectuer un basculement planifié à tout moment, une fois que vos conditions locales sont plus favorables.

**Pour annuler un basculement planifié** :

1. Accédez à la machine dans le coffre Recovery Services et sélectionnez **Annuler le basculement**.
2. Cliquez sur **OK**.
3. Veillez à lire les informations relatives à l’exécution de l’opération d’*annulation du basculement*.

Si des problèmes empêchent Azure Site Recovery d’annuler correctement la tâche ayant échoué, suivez les étapes recommandées fournies dans la tâche. Après avoir suivi les recommandations, relancez la tâche d’annulation.

L’opération de basculement planifié précédente sera annulée. La machine dans Azure sera rétablie à l’état qui était le sien juste avant le déclenchement du *basculement planifié*.

Pour un basculement planifié, après avoir détaché les disques de la machine virtuelle de l’appliance, nous en prenons un instantané avant de la mettre sous tension.

Si la machine virtuelle ne démarre pas correctement ou si une application n’est pas disponible, ou si pour une raison quelconque, vous décidez d’annuler le basculement planifié et de réessayer, alors :

1. Nous annulons toutes les modifications apportées.

2. Nous rétablissons les disques sur l’état qui était le leur avant la mise sous tension, en utilisant les instantanés pris précédemment.

3. Enfin, nous rattachons les disques à l’appliance et reprenons la réplication.

Ce comportement diffère de celui dans l’architecture héritée/classique.

- À l’aide de la préversion, vous pouvez réexécuter l’opération de restauration à un moment ultérieur.

- Dans l’architecture héritée, vous ne pouvez pas annuler et réessayer la restauration : si la machine virtuelle ne démarre pas ou si l’application ne démarre pas ou pour toute autre raison.  


> [!NOTE]
> Seul le basculement planifié à partir d’Azure vers un emplacement local peut être annulé. Il est impossible d’annuler le basculement à partir d’un emplacement local vers Azure.

### <a name="planned-failover---failure"></a>Échec du basculement planifié

Si le basculement planifié échoue, Azure Site Recovery lance automatiquement une tâche pour annuler la tâche qui a échoué et récupère l’état de la machine tel qu’il était juste avant le basculement planifié.

En cas d’échec de l’annulation de la dernière tâche de basculement planifié, Azure Site Recovery vous invite à lancer l’annulation manuellement.

Ces informations sont fournies dans le cadre de l’opération de basculement planifié ayant échoué et en tant que problème d’intégrité de l’élément répliqué.

Si le problème persiste, contactez le support Microsoft. **Ne désactivez pas** la réplication.

## <a name="re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover"></a>Protéger à nouveau la machine locale sur Azure après un basculement planifié réussi

Une fois le basculement planifié réussi, la machine est active dans votre environnement local. Pour protéger votre machine à l’avenir, assurez-vous qu’elle est répliquée sur Azure (à nouveau protégée).

Pour ce faire, accédez à la machine > **Protéger à nouveau**, sélectionnez l’appliance de votre choix, sélectionnez la stratégie de réplication et continuez.

Après l’activation réussie de la réplication et la réplication initiale, des points de récupération sont générés pour assurer la continuité de l’activité contre les interruptions indésirables.

## <a name="next-steps"></a>Étapes suivantes

Une fois le basculement effectué, reprotégez les machines virtuelles Azure locales. Une fois les machines virtuelles reprotégées et répliquées sur le site local, effectuez une restauration automatique à partir d’Azure quand vous êtes prêt.

> [!div class="nextstepaction"]
> [Reprotéger les machines virtuelles Azure](vmware-azure-reprotect.md)
> [Effectuer une restauration automatique à partir d’Azure](vmware-azure-failback.md)
