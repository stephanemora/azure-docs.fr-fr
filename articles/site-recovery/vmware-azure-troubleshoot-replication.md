---
title: Résoudre des problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article fournit des informations sur la résolution de problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 3221b2bd18b8b0756f280d88fffc6016d0498b8f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924828"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Résoudre les problèmes de réplication pour les serveurs physiques et machines virtuelles VMware

Cet article décrit certains problèmes courants et les erreurs spécifiques, vous pouvez rencontrer lorsque vous répliquez des machines virtuelles de VMware en local et des serveurs physiques vers Azure à l’aide [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Étape 1 : Surveiller l’intégrité de serveur de processus

Utilisations de la récupération de site le [serveur de processus](vmware-physical-azure-config-process-server-overview.md#process-server) pour recevoir et optimiser les données répliquées et envoyez-le à Azure.

Nous vous recommandons de surveiller l’intégrité des serveurs de processus dans le portail, pour s’assurer qu’ils sont connectés et fonctionne correctement et que la réplication progresse pour les machines sources associées au serveur de processus.

- [En savoir plus sur](vmware-physical-azure-monitor-process-server.md) surveillance des serveurs de processus.
- [Passer en revue les meilleures pratiques](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Résoudre les problèmes](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) traiter l’intégrité du serveur.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Étape 2 : Résoudre les problèmes de connectivité et de réplication

Échecs de réplication initiales et actuelles sont souvent provoquées par des problèmes de connectivité entre le serveur source et le serveur de processus ou entre le serveur de processus et Azure. 

Pour résoudre ces problèmes, [résoudre les problèmes de connectivité et réplication](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Étape 3 : Résoudre les problèmes des machines sources qui ne sont pas disponibles pour la réplication

Lorsque vous tentez de sélectionner la machine source pour activer la réplication avec Site Recovery, il se peut que cette machine ne soit pas disponible pour l’une des raisons suivantes :

* **Deux machines virtuelles avec le même UUID d’instance** : Si deux machines virtuelles sous le vCenter ont le même UUID d’instance, la première machine découverte par le serveur de configuration apparaît sur le portail Azure. Pour résoudre ce problème, assurez-vous que deux machines virtuelles n’ont pas le même UUID d’instance. Ce scénario se rencontre communément dans les cas où une machine virtuelle sauvegarde devienne actif et est enregistrée dans nos enregistrements de découverte. Consultez [Azure Site Recovery, WMware vers Azure : comment nettoyer les entrées en double ou périmées](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) pour résoudre le problème.
* **Informations d’identification de l’utilisateur vCenter incorrectes** : Vérifiez que vous avez ajouté les informations d’identification de vCenter correctes lors de la configuration du serveur de configuration en utilisant le modèle OVF ou une configuration unifiée. Pour vérifier les informations d’identification que vous avez ajoutées pendant la configuration, voir [Modifier les informations d’identification pour la découverte automatique](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilèges insuffisants pour vCenter** : Si les autorisations fournies pour accéder à vCenter ne sont pas les autorisations requises, la découverte des machines virtuelles pourrait échouer. Assurez-vous que les autorisations décrites dans [Préparer un compte pour la découverte automatique](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) sont ajoutées au compte d’utilisateur de vCenter.
* **Serveurs d’administration Azure Site Recovery** : Si la machine virtuelle est utilisée comme serveur d’administration sous un ou plusieurs de ces rôles (serveur de configuration, serveur de processus avec scale-out, serveur cible maître), vous ne pourrez pas choisir la machine virtuelle à partir du portail. Les serveurs d’administration ne peuvent pas être répliqués.
* **Machine virtuelle déjà protégée/basculée par le biais des services Azure Site Recovery** : si la machine virtuelle est déjà protégée ou basculée par le biais de Site Recovery, vous ne pouvez pas la sélectionner à des fins de protection dans le portail. Vérifiez que la machine virtuelle que vous recherchez sur le portail n’est pas déjà protégée par un autre utilisateur ou sous un autre abonnement.
* **vCenter non connecté** : Vérifiez si vCenter est dans un état connecté. Pour cela, accédez au coffre Recovery Services > Infrastructure Site Recovery > Serveurs de configuration, puis cliquez sur un serveur de configuration. Un panneau s’ouvre à droite avec des détails sur les serveurs associés. Vérifiez si vCenter est connecté. Si elle est dans un état « Non connecté », corrigez le problème, puis [actualiser le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) sur le portail. Après cela, la machine virtuelle est listée dans le portail.
* **ESXi hors tension** : Si l’hôte ESXi sous lequel la machine virtuelle réside est dans un état hors tension, la machine virtuelle n’est pas listée ou ne peut pas être sélectionnée dans le portail Azure. Mettez l’hôte ESXi sous tension, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) dans le portail. Après cela, la machine virtuelle est listée dans le portail.
* **Redémarrage en attente** : si un redémarrage est en attente sur la machine virtuelle, vous ne pouvez pas sélectionner la machine dans le portail Azure. Veillez à effectuer les activités de redémarrage en attente, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Après cela, la machine virtuelle est listée dans le portail.
* **IP introuvable** : Si une adresse IP valide n’est pas associée à la machine virtuelle, vous ne pouvez pas sélectionner la machine dans le portail Azure Veillez à affecter une adresse IP valide à la machine virtuelle, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Après cela, la machine virtuelle est listée dans le portail.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Résoudre les problèmes de machines virtuelles protégées grisées dans le portail

Les machines virtuelles répliquées sous Azure Site Recovery sont indisponibles sur le portail Azure s’il existe des entrées en double dans le système. Pour savoir comment supprimer des entrées périmées et résoudre le problème, consultez [Azure Site Recovery, WMware vers Azure : comment nettoyer les entrées en double ou périmées](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Erreurs courantes et solutions

### <a name="initial-replication-issues-error-78169"></a>Problèmes de réplication initiale [erreur 78169]

Sur un ci-dessus garantir qu’il existe aucune connectivité, la bande passante ou l’heure de synchroniser les problèmes connexes, vérifiez que :

- Aucun logiciel antiviru ne bloque Azure Site Recovery. En savoir plus [plus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sur les exclusions de dossiers requises pour Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Points de récupération cohérents d’application [erreur 78144] manquants

 Cela se produit en raison de problèmes avec Volume Shadow copy Service (VSS). Pour résoudre ce problème : 
 
- Vérifiez que la version installée de l’agent Azure Site Recovery est au moins 9.22.2. 
- Vérifiez que le fournisseur VSS est installé en tant que service dans les Services Windows et vérifiez la console de Service de composant MMC pour vérifier que le fournisseur VSS Azure Site Recovery est répertorié.
- Si le fournisseur VSS n’est pas installé, consultez le [Échec de l’installation article sur le dépannage](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Si VSS est désactivé,
    - Vérifiez que le type de démarrage du service fournisseur VSS est défini sur **automatique**.
    - Redémarrez les services suivants :
        - Service VSS
        - Fournisseur VSS d’Azure Site Recovery
        - Service VDS

### <a name="source-machines-with-high-churn-error-78188"></a>Ordinateurs source avec une évolution élevée [erreur 78188]

Causes possibles :
- Le taux de modification de données (octets/s d’écriture) sur les disques répertoriés de la machine virtuelle est supérieur à la [Azure Site Recovery prises en charge les limites](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) pour le type de compte de stockage cible la réplication.
- Il existe un pic soudain dans le taux d’évolution en raison de quelle quantité élevée de données est en attente pour le chargement.

Pour résoudre le problème :
- Assurez-vous que le type de compte de stockage cible (Standard ou Premium) est approvisionné selon les besoins de taux d’ATTRITION à la source.
- Si l’activité observée est temporaire, attendez quelques heures pour le chargement des données en attente pour rattraper le retard et créer des points de récupération.
- Si le problème persiste, utilisez la récupération de Site [Planificateur de déploiement](site-recovery-deployment-planner.md#overview) pour aider à planifier la réplication.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Machines sources avec aucune pulsation [erreur 78174]

Cela se produit lorsque l’agent de mobilité Azure Site Recovery sur la Machine Source ne communique pas avec le serveur de Configuration (CS).

Pour résoudre ce problème, procédez comme suit pour vérifier la connectivité réseau à partir de la machine virtuelle source vers le serveur de configuration :

1. Vérifiez que l’ordinateur Source est en cours d’exécution.
2. Connectez-vous à l’ordinateur Source à l’aide d’un compte disposant des privilèges d’administrateur.
3. Vérifiez que les services suivants sont en cours d’exécution et si ne pas redémarrer les services :
   - Svagents (InMage Scout VX Agent)
   - Service d’application InMage Scout
4. Sur la Machine Source, examinez les journaux à l’emplacement pour les détails de l’erreur :

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Serveur de traitement avec aucune pulsation [erreur 806]
Au cas où aucune pulsation à partir du serveur de processus, vérifiez que :
1. PS Machine virtuelle est en cours d’exécution
2. Vérifiez suivante ouvre une session sur le serveur de traitement pour les détails de l’erreur :

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Serveur cible maître avec aucune pulsation [erreur 78022]

Cela se produit lorsque l’agent de mobilité Azure Site Recovery sur le serveur cible maître ne communique pas avec le serveur de Configuration.

Pour résoudre ce problème, procédez comme suit pour vérifier l’état du service :

1. Vérifiez que la machine virtuelle cible de Master est en cours d’exécution.
2. Connectez-vous à la machine virtuelle cible maître à l’aide d’un compte disposant des privilèges d’administrateur.
    - Vérifiez que le service svagents est en cours d’exécution. Si elle est en cours d’exécution, redémarrez le service
    - Vérifiez les journaux à l’emplacement pour les détails de l’erreur :
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide, publiez votre question sur le [forum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Nous avons une communauté active et l’un de nos ingénieurs peut vous aider.
