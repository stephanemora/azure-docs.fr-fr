---
title: Résoudre des problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article fournit des informations sur la résolution de problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: mayg
ms.openlocfilehash: c005dcee78e2a9338dc7a816e06d9a78a2f355b6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491674"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Résoudre les problèmes de réplication pour les serveurs physiques et machines virtuelles VMware

Cet article décrit certains problèmes courants et erreurs spécifiques que vous pouvez rencontrer lorsque vous répliquez des machines virtuelles et serveurs physiques VMware locaux sur Azure avec [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Étape 1 : Surveiller l’intégrité du serveur de processus

Site Recovery utilise le [serveur de processus](vmware-physical-azure-config-process-server-overview.md#process-server) pour recevoir et optimiser les données répliquées, ainsi que pour les envoyer à Azure.

Nous vous recommandons de surveiller l’intégrité des serveurs de processus dans le portail, pour vous assurer qu’ils sont connectés et fonctionnent correctement, et que la réplication progresse pour les machines sources associées au serveur de processus.

- [En savoir plus](vmware-physical-azure-monitor-process-server.md) sur la surveillance des serveurs de processus
- [Passer en revue les meilleures pratiques](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Détecter un problème](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) d’intégrité du serveur de processus

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Étape 2 : Détecter les problèmes de connectivité et de réplication

Les défaillances de réplication initiales et en cours résultent souvent de problèmes de connectivité entre le serveur source et le serveur de processus, ou entre ce dernier et Azure. 

Pour résoudre ces difficultés, [détectez les problèmes de connectivité et de réplication](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Étape 3 : Détecter les problèmes de machines sources qui ne sont pas disponibles pour la réplication

Lorsque vous tentez de sélectionner la machine source pour activer la réplication avec Site Recovery, il se peut que cette machine ne soit pas disponible pour l’une des raisons suivantes :

* **Deux machines virtuelles avec le même UUID d’instance** : Si deux machines virtuelles sous le vCenter ont le même UUID d’instance, la première machine découverte par le serveur de configuration apparaît sur le portail Azure. Pour résoudre ce problème, assurez-vous que deux machines virtuelles n’ont pas le même UUID d’instance. Ce scénario est courant dans les cas où une machine virtuelle de sauvegarde devient active et est connectée à nos enregistrements de découverte. Consultez [Azure Site Recovery, WMware vers Azure : comment nettoyer les entrées en double ou périmées](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) pour résoudre le problème.
* **Informations d’identification de l’utilisateur vCenter incorrectes** : Vérifiez que vous avez ajouté les informations d’identification de vCenter correctes lors de la configuration du serveur de configuration en utilisant le modèle OVF ou une configuration unifiée. Pour vérifier les informations d’identification que vous avez ajoutées pendant la configuration, voir [Modifier les informations d’identification pour la découverte automatique](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilèges insuffisants pour vCenter** : Si les autorisations fournies pour accéder à vCenter ne sont pas les autorisations requises, la découverte des machines virtuelles pourrait échouer. Assurez-vous que les autorisations décrites dans [Préparer un compte pour la découverte automatique](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) sont ajoutées au compte d’utilisateur de vCenter.
* **Serveurs d’administration Azure Site Recovery** : Si la machine virtuelle est utilisée comme serveur d’administration sous un ou plusieurs de ces rôles (serveur de configuration, serveur de processus avec scale-out, serveur cible maître), vous ne pourrez pas choisir la machine virtuelle à partir du portail. Les serveurs d’administration ne peuvent pas être répliqués.
* **Machine virtuelle déjà protégée/basculée par le biais des services Azure Site Recovery** : si la machine virtuelle est déjà protégée ou basculée par le biais de Site Recovery, vous ne pouvez pas la sélectionner à des fins de protection dans le portail. Vérifiez que la machine virtuelle que vous recherchez sur le portail n’est pas déjà protégée par un autre utilisateur ou sous un autre abonnement.
* **vCenter non connecté** : Vérifiez si vCenter est dans un état connecté. Pour cela, accédez au coffre Recovery Services > Infrastructure Site Recovery > Serveurs de configuration, puis cliquez sur un serveur de configuration. Un panneau s’ouvre à droite avec des détails sur les serveurs associés. Vérifiez si vCenter est connecté. S’il présente l’état « Non connecté », résolvez le problème, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) dans le portail. Après cela, la machine virtuelle est listée dans le portail.
* **ESXi hors tension** : Si l’hôte ESXi sous lequel la machine virtuelle réside est dans un état hors tension, la machine virtuelle n’est pas listée ou ne peut pas être sélectionnée dans le portail Azure. Mettez l’hôte ESXi sous tension, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) dans le portail. Après cela, la machine virtuelle est listée dans le portail.
* **Redémarrage en attente** : si un redémarrage est en attente sur la machine virtuelle, vous ne pouvez pas sélectionner la machine dans le portail Azure. Veillez à effectuer les activités de redémarrage en attente, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Après cela, la machine virtuelle est listée dans le portail.
* **IP introuvable** : Si une adresse IP valide n’est pas associée à la machine virtuelle, vous ne pouvez pas sélectionner la machine dans le portail Azure Veillez à affecter une adresse IP valide à la machine virtuelle, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Après cela, la machine virtuelle est listée dans le portail.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Détecter les problèmes de machines virtuelles protégées qui apparaissent en grisé dans le portail

Les machines virtuelles répliquées sous Azure Site Recovery sont indisponibles sur le portail Azure s’il existe des entrées en double dans le système. Pour savoir comment supprimer des entrées périmées et résoudre le problème, consultez [Azure Site Recovery, WMware vers Azure : comment nettoyer les entrées en double ou périmées](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Erreurs courantes et solutions

### <a name="initial-replication-issues-error-78169"></a>Problèmes de réplication initiale [erreur 78169]

Après vous être assuré de l’absence de problèmes de connectivité, de bande passante ou de synchronisation de l’heure, vérifiez les points suivants :

- Aucun antivirus ne bloque Azure Site Recovery. Apprenez-en [plus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sur les exclusions de dossier requises pour Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Points de récupération de cohérence des applications manquants [erreur 78144]

 Cette erreur découle de l’existence de problèmes avec le service VSS. Pour résoudre ce problème : 
 
- Vérifiez que la version installée de l’agent Azure Site Recovery n’est pas inférieure à la version 9.22.2. 
- Vérifiez que le fournisseur VSS est installé en tant que service dans les services Windows, et vérifiez également que le fournisseur VSS Azure Site Recovery est répertorié dans la console MMC de services de composant.
- Si le fournisseur VSS n’est pas installé, consultez [l’article concernant la détection des échecs d’installation](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Si le service VSS est désactivé :
    - Vérifiez que le type de démarrage du service fournisseur VSS est défini sur **Automatique**.
    - Redémarrez les services suivants :
        - Service VSS
        - Fournisseur VSS d’Azure Site Recovery
        - Service VDS (Virtual Disk Service)

- Si vous exécutez des charges de travail SQL ou Exchange, recherchez les échecs dans les journaux de ces créateurs d’applications. Les erreurs fréquentes et leur résolution sont répertoriées dans les articles suivants :
    -  [L’option de fermeture automatique de la base de données SQL Server est définie sur TRUE](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 levant une erreur non renouvelable](https://support.microsoft.com/help/4504103)
    - [Problème connu dans SQL Server 2016 et 2017](https://support.microsoft.com/help/4493364)
    - [Problème courant avec les serveurs Exchange Server 2013 et 2016](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>Machines sources à taux d’évolution élevé [erreur 78188]

Causes possibles :
- Le taux de modification des données (octets écrits/s) sur les disques répertoriés de la machine virtuelle dépasse les [limites prises en charge par Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) pour le type de compte de stockage cible de réplication.
- Le taux d’évolution des données connaît un pic soudain à cause duquel un important volume de données est en attente de chargement.

Pour résoudre le problème :
- Assurez-vous que le type de compte de stockage cible (Standard ou Premium) est approvisionné conformément aux exigences de taux d’évolution des données au niveau de la machine source.
- Si le taux d’évolution observé est temporaire, attendez quelques heures que le chargement des données en attente rattrape son retard et crée des points de récupération.
- Si le problème persiste, utilisez le [Planificateur de déploiement](site-recovery-deployment-planner.md#overview) Site Recovery pour faciliter la planification de la réplication.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Machines sources dépourvues de pulsation [erreur 78174]

Cette erreur se produit lorsque l’agent Mobilité Azure Site Recovery sur la machine source ne communique pas avec le serveur de configuration (CS).

Pour résoudre ce problème, procédez comme suit pour vérifier la connectivité réseau entre la machine virtuelle source et le serveur de configuration :

1. Vérifiez que la machine source est en cours d’exécution.
2. Connectez-vous à la machine source à l’aide d’un compte disposant de privilèges d’administrateur.
3. Vérifiez que les services ci-après sont en cours d’exécution et, dans le cas contraire, redémarrez-les :
   - Svagents (InMage Scout VX Agent)
   - Service d’application InMage Scout
4. Sur la machine source, examinez les journaux à l’emplacement ci-après pour obtenir les détails de l’erreur :

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Serveur de processus dépourvu de pulsation [erreur 806]
Si le serveur de processus est dépourvu de pulsation, vérifiez les points suivants :
1. La machine virtuelle du serveur de processus est opérationnelle.
2. Consultez les journaux ci-après sur le serveur de processus pour obtenir les détails de l’erreur :

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Serveur cible maître dépourvu de pulsation [erreur 78022]

Cette erreur se produit lorsque l’agent Mobilité Azure Site Recovery sur la cible maître ne communique pas avec le serveur de configuration.

Pour résoudre ce problème, vérifiez l’état du service en procédant comme suit :

1. Vérifiez que la machine virtuelle cible maître est en cours d’exécution.
2. Connectez-vous à la machine virtuelle cible maître à l’aide d’un compte disposant de privilèges d’administrateur.
    - Vérifiez que le service svagents est en cours d’exécution. S’il l’est, redémarrez-le.
    - Vérifiez les journaux à l’emplacement ci-après pour obtenir les détails de l’erreur :
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide, publiez votre question sur le [forum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Nous avons une communauté active et l’un de nos ingénieurs peut vous aider.
