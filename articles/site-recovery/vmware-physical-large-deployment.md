---
title: Configurer la récupération d’urgence vers Azure pour un grand nombre de machines virtuelles VMware ou serveurs physiques avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment configurer la récupération d’urgence vers Azure pour un grand nombre de machines virtuelles de VMware locales ou des serveurs physiques avec Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: 593a82221ec94e43d50b7382ba89053d5f68864a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805920"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configurer la récupération d’urgence à l’échelle pour les serveurs de machines virtuelles VMware/physique

Cet article explique comment configurer la récupération d’urgence vers Azure pour un grand nombre (> 1000) de machines virtuelles de VMware locales ou des serveurs physiques dans votre environnement de production, à l’aide de la [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="define-your-bcdr-strategy"></a>Définir votre stratégie BCDR

Dans le cadre de votre continuité d’activité et de la stratégie de récupération d’urgence, vous définissez des objectifs de point de récupération (RPO) et objectifs de temps de récupération (RTO) pour vos applications métier et les charges de travail. RTO mesure la durée de temps et de service au niveau dans lequel une application métier ou un processus doit être restauré et disponible, afin d’éviter les problèmes de continuité d’activité.
- Site Recovery assure la réplication continue pour les machines virtuelles VMware et des serveurs physiques et un [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) pour RTO.
- Lorsque vous planifiez une récupération d’urgence à grande échelle pour les machines virtuelles VMware et de déterminer les ressources Azure que vous avez besoin, vous pouvez spécifier une valeur RTO qui sera utilisée pour les calculs de capacité.


## <a name="best-practices"></a>Bonnes pratiques

Certaines meilleures pratiques générales pour la récupération d’urgence à grande échelle. Ces meilleures pratiques sont abordés plus en détail dans les sections suivantes du document.

- **Identifier les exigences de la cible**: Estimer les besoins de capacité et de ressources dans Azure avant de définir la récupération d’urgence.
- **Planifier pour les composants Site Recovery**: Déterminez quels composants de Site Recovery (serveur de configuration, serveurs de processus) que vous avez besoin répondre à votre capacité estimée.
- **Configurer un ou plusieurs serveurs de processus de scale-out**: N’utilisez pas le serveur de processus qui s’exécute par défaut sur le serveur de configuration. 
- **Exécuter les dernières mises à jour**: L’équipe de Site Recovery de nouvelles versions des composants de Site Recovery sur une base régulière, et vous devez vous assurer que vous exécutez des versions les plus récentes. Pour vous aider à qui, suivre [Nouveautés](site-recovery-whats-new.md) des mises à jour, et [activer et installer les mises à jour](service-updates-how-to.md) dès leur parution.
- **Surveiller de manière proactive**: Que vous obtenez une récupération d’urgence soit opérationnel, vous devez surveiller proactivement l’état et l’intégrité des machines répliquées et des ressources d’infrastructure.
- **Exercices de récupération d’urgence**: Vous devez exécuter des exercices de récupération après sinistre sur une base régulière. Ils n’ont aucun impact sur votre environnement de production, mais aident à garantir que le basculement vers Azure fonctionnera comme prévu si nécessaire.



## <a name="gather-capacity-planning-information"></a>Collecter des informations de planification de capacité

Collecter des informations sur votre environnement local, pour aider à évaluer et estimer les que besoins de votre capacité cible (Azure).
- Pour VMware, exécutez le Planificateur de déploiement pour les machines virtuelles VMware pour ce faire.
- Pour les serveurs physiques, collectez les informations manuellement.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Exécuter Deployment Planner pour les machines virtuelles VMware

Le Planificateur de déploiement vous aide à recueillir des informations sur votre environnement local de VMware.

- Exécuter Deployment Planner pendant une période qui représente l’activité typique pour vos machines virtuelles. Cela générera des estimations plus précises et des recommandations.
- Nous vous recommandons d’exécuter le Planificateur de déploiement sur l’ordinateur serveur de configuration, dans la mesure où le module calcule le débit à partir du serveur sur lequel il est en cours d’exécution. [En savoir plus](site-recovery-vmware-deployment-planner-run.md#get-throughput) sur la mesure de débit.
- Si vous n’avez pas encore configuré un serveur de configuration :
    - [Obtenir une vue d’ensemble](vmware-physical-azure-config-process-server-overview.md) des composants Site Recovery.
    - [Configurer un serveur de configuration](vmware-azure-deploy-configuration-server.md), afin d’exécuter le Planificateur de déploiement sur celui-ci.

Ensuite, exécutez le planificateur comme suit :

1. [En savoir plus sur](site-recovery-deployment-planner.md) le Planificateur de déploiement. Vous pouvez télécharger la dernière version à partir du portail, ou [Téléchargez-la directement](https://aka.ms/asr-deployment-planner).
2. Examinez le [conditions préalables](site-recovery-deployment-planner.md#prerequisites) et [dernières mises à jour](site-recovery-deployment-planner-history.md) pour le Planificateur de déploiement, et [Téléchargez et extrayez](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) l’outil.
3. [Exécuter Deployment Planner](site-recovery-vmware-deployment-planner-run.md) sur le serveur de configuration.
4. [Générer un rapport](site-recovery-vmware-deployment-planner-run.md#generate-report) permet de synthétiser des estimations et des recommandations.
5. Analyser la [signaler recommandations](site-recovery-vmware-deployment-planner-analyze-report.md) et [estimations de coût](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Par défaut, l’outil est configuré pour le profil et génère des rapports pour jusqu'à 1 000 machines virtuelles. Vous pouvez modifier cette limite en augmentant la valeur de clé MaxVMsSupported dans le fichier ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Planifier les exigences de capacité et la cible (Azure)

À l’aide de vos estimations collectées et les recommandations, vous pouvez planifier pour la capacité et les ressources cibles. Si vous avez exécuté le Planificateur de déploiement pour les machines virtuelles VMware, vous pouvez utiliser un numéro de la [signaler les recommandations](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) pour vous aider.

- **Machines virtuelles compatibles**: Ce numéro permet d’identifier le nombre de machines virtuelles qui sont prêts pour la récupération d’urgence vers Azure. Recommandations sur la bande passante réseau et de cœurs Azure sont basées sur ce nombre.
- **Bande passante réseau requise**: Notez la bande passante que nécessaire pour la réplication delta de machines virtuelles compatibles. 
    - Lorsque vous exécutez le planificateur vous spécifiez le RPO souhaité en quelques minutes. Les recommandations vous montrent la bande passante nécessaire pour atteindre ce RPO 100 % et 90 % du temps. 
    - Les recommandations de la bande passante réseau prennent en compte la bande passante nécessaire pour le nombre total de serveurs de configuration et les serveurs de processus recommandés dans le planificateur.
- **Azure cœurs nécessaires**: Notez le nombre de cœurs que vous avez besoin dans la région cible Azure, en fonction du nombre de machines virtuelles compatibles. Si vous n’avez pas assez de cœurs, au moment du basculement Site Recovery ne sont pas être en mesure de créer les machines virtuelles Azure requis.
- **Taille de lot de machine virtuelle recommandée**: La taille de lot recommandée est basée sur la capacité à terminer la réplication initiale pour le lot dans les 72 heures par défaut, tout en répondant à un RPO de 100 %. La valeur d’heure peut être modifiée.

Vous pouvez utiliser ces recommandations pour planifier les ressources Azure, la bande passante réseau et le traitement par lot de machine virtuelle.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planifier des quotas et des abonnements Azure

Nous souhaitons vous assurer que les quotas disponibles dans l’abonnement cible sont suffisantes pour gérer le basculement.

**Tâche** | **Détails** | **Action**
--- | --- | ---
**Vérification de cœurs** | Si les cœurs dans le quota disponible n’égale ou dépasse le nombre total de cibles au moment du basculement, les basculements échouera. | Pour les machines virtuelles VMware, vérifiez de qu'avoir assez de cœurs dans l’abonnement cible pour répondre à la recommandation de core Planificateur de déploiement.<br/><br/> Pour les serveurs physiques, vérifiez que les cœurs Azure répondent à vos estimations manuelles.<br/><br/> Pour vérifier les quotas, dans le portail Azure > **abonnement**, cliquez sur **utilisation + quotas**.<br/><br/> [En savoir plus](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) sur l’augmentation des quotas.
**Vérifiez les limites de basculement** | Le nombre de basculements ne doit pas dépasser les limites de basculement Site Recovery. |  Si les basculements dépassent les limites, vous pouvez ajouter des abonnements et basculer à plusieurs abonnements ou augmenter le quota pour un abonnement. 


### <a name="failover-limits"></a>Limites de basculement

Les limites d’indiquent le nombre de basculements sont prises en charge par Site Recovery dans une heure, en supposant que les trois disques par machine.

Ce que répond-elle moyenne ? Pour démarrer une machine virtuelle Azure, Azure requiert certains pilotes se trouver dans un état de démarrage de démarrage et des services, comme DHCP pour être configuré pour démarrer automatiquement.
- Machines qui sont conformes aura déjà ces paramètres en place.
- Pour les ordinateurs exécutant Windows, vous pouvez proactive vérifier la conformité et les rendre conformes si nécessaire. [Plus d’informations](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)
- Les machines Linux sont uniquement mises en conformité au moment du basculement.

**Machine est compatible avec Azure ?** | **Limites des machines virtuelles Azure (basculement de disque géré)**
--- | --- 
Oui | 2000
Non | 1 000

- Limites supposent autrement minimales d’autres travaux sont en cours d’exécution dans la région cible pour l’abonnement.
- Certaines régions Azure sont plus petites et peuvent avoir des limites légèrement inférieures.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planifier l’infrastructure et connectivité de machine virtuelle

Après le basculement vers Azure, vous avez besoin de vos charges de travail pour fonctionner comme en local et permet aux utilisateurs d’accéder aux charges de travail en cours d’exécution sur les machines virtuelles Azure.

- [En savoir plus](site-recovery-active-directory.md#test-failover-considerations) environ basculer votre infrastructure locale Active Directory ou DNS vers Azure.
- [En savoir plus](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sur la préparation pour se connecter aux machines virtuelles Azure après le basculement.



## <a name="plan-for-source-capacity-and-requirements"></a>Planifier la capacité de la source et la configuration requise

Il est important que vous avez suffisamment serveurs de configuration et les serveurs de traitement de montée en puissance pour répondre aux besoins en capacité. Lorsque vous commencez votre déploiement à grande échelle, commencez avec un seul serveur de configuration et un serveur de processus de montée en puissance unique. Que vous atteignez les limites prescrites, ajouter des serveurs supplémentaires.

>[!NOTE]
> Pour les machines virtuelles VMware, le Planificateur de déploiement fait quelques recommandations concernant les serveurs de configuration et de processus que vous avez besoin. Nous vous recommandons d’utiliser les tables incluses dans les procédures suivantes, au lieu de suivre la recommandation du Planificateur de déploiement. 


## <a name="set-up-a-configuration-server"></a>Configurer un serveur de configuration
 
La capacité du serveur configuration est affectée par le nombre de machines répliquées et non par les données du taux d’activité. Pour déterminer si vous avez besoin de serveurs de configuration supplémentaires, utilisez ces limites définies de la machine virtuelle.

**UC** | **Mémoire** | **Disque cache** | **Limite de l’ordinateur de réplication**
 --- | --- | --- | ---
8 processeurs virtuels<br> 2 sockets * 4 cœurs @ 2,5 Ghz | 16 Go | 600 TO | Jusqu'à 550 machines<br> Part du principe que chaque ordinateur dispose de trois disques de 100 Go.

- Ces limites sont basées sur un serveur de configuration défini à l’aide d’un modèle OVF.
- Les limites supposent que vous n’utilisez pas le serveur de processus qui s’exécute par défaut sur le serveur de configuration.

Si vous avez besoin ajouter un nouveau serveur de configuration, suivez ces instructions :

- [Configurer un serveur de configuration](vmware-azure-deploy-configuration-server.md) pour la récupération d’urgence de VMware VM, à l’aide d’un modèle OVF.
- [Configurer un serveur de configuration](physical-azure-set-up-source.md) manuellement pour les serveurs physiques, ou pour les déploiements VMware qui ne peut pas utiliser un modèle OVF.

Lorsque vous configurez un serveur de configuration, sachez que :

- Lorsque vous configurez un serveur de configuration, il est important de tenir compte de l’abonnement et le coffre dans lequel il réside, étant donné que celles-ci ne doivent pas être modifiées après l’installation. Si vous n’avez pas besoin de modifier le coffre, vous devez dissocier le serveur de configuration à partir du coffre et l’enregistrer à nouveau. Cela arrête la réplication des machines virtuelles dans le coffre.
- Si vous souhaitez configurer un serveur de configuration avec plusieurs cartes réseau, vous devez le faire au cours de l’ensemble. Vous ne pouvez pas le faire après l’inscription du serveur de configuration dans le coffre.

## <a name="set-up-a-process-server"></a>Configurer un serveur de processus

Capacité de serveur de processus est affectée par les taux de perte de données et non par le nombre d’ordinateurs activées pour la réplication.

- Pour les déploiements à grandes échelle, vous devez toujours au moins un serveur de processus de scale-out.
- Pour déterminer si vous disposiez de serveurs supplémentaires, utilisez le tableau suivant.
- Nous vous recommandons d’ajouter un serveur avec la spécification la plus élevée. 


**UC** | **Mémoire** | **Disque cache** | **Taux d’activité**
 --- | --- | --- | --- 
12 processeurs virtuels<br> 2 sockets * 6 cœurs @ 2,5 Ghz | 24 Go | 1 Go | Jusqu'à 2 To jour

Configurer le serveur de processus, procédez comme suit :

1. Examinez les [conditions préalables](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installer le serveur dans le [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), ou à partir de la [ligne de commande](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configurer les machines répliquées pour utiliser le nouveau serveur. Si vous avez déjà des machines répliquées :
    - Vous pouvez [déplacer](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) une charge de travail de serveur de processus vers le nouveau serveur de processus.
    - Vous pouvez également [déplacer](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) des machines virtuelles spécifiques vers le nouveau serveur de processus.



## <a name="enable-large-scale-replication"></a>Activer la réplication à grande échelle

Après la planification de capacité et le déploiement de l’infrastructure et les composants requis, activez la réplication pour un grand nombre de machines virtuelles.

1. Trier les ordinateurs en lots. Vous activez la réplication pour les machines virtuelles au sein d’un lot et ensuite passez le lot suivant.

    - Pour les machines virtuelles VMware, vous pouvez utiliser la [taille de lot de machine virtuelle recommandée](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) dans le rapport du Planificateur de déploiement.
    - Pour les machines physiques, nous vous recommandons de qu'identifier lots basés sur les ordinateurs qui ont une taille et la quantité de données similaires et sur le débit réseau disponible. L’objectif est de machines de lot sont susceptibles de terminer leur réplication initiale en autour de la même quantité de temps.
    
2. Si l’évolution du disque pour un ordinateur est élevé, ou dépasse les limites dans thePlanner de déploiement, vous pouvez déplacer les fichiers non critiques, que vous n’avez pas besoin de répliquer (par exemple, les vidages du journal ou des fichiers temporaires) hors tension la machine. Pour les machines virtuelles VMware, vous pouvez déplacer ces fichiers vers un disque distinct, puis [exclure ce disque](vmware-azure-exclude-disk.md) de la réplication.
3. Avant d’activer la réplication, vérifiez que les ordinateurs répondent aux [impératifs de réplication](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configurer une stratégie de réplication pour [des machines virtuelles VMware](vmware-azure-set-up-replication.md#create-a-policy) ou [serveurs physiques](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Activez la réplication pour [des machines virtuelles VMware](vmware-azure-enable-replication.md) ou [serveurs physiques](physical-azure-disaster-recovery.md#enable-replication). Ceci lance la réplication initiale pour les ordinateurs sélectionnés.

## <a name="monitor-your-deployment"></a>Surveiller votre déploiement

Une fois que vous lancez la réplication pour le premier lot de machines virtuelles, démarrer la surveillance de votre déploiement comme suit :  

1. Affecter un administrateur de la récupération d’urgence pour surveiller l’état d’intégrité des machines répliquées.
2. [Surveiller les événements](site-recovery-monitor-and-troubleshoot.md) pour les éléments répliqués et l’infrastructure.
3. [Surveiller l’intégrité](vmware-physical-azure-monitor-process-server.md) de vos serveurs de traitement de montée en puissance.
4. Inscrivez-vous pour recevoir [notifications par courrier électronique](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) des événements, pour faciliter la surveillance.
5. Mener regular [exercices de récupération d’urgence](site-recovery-test-failover-to-azure.md)pour vous assurer que tout fonctionne comme prévu.


## <a name="plan-for-large-scale-failovers"></a>Plan pour les basculements à grande échelle

En cas d’urgence, vous devrez peut-être faire basculer un grand nombre de machines/charges de travail vers Azure. Préparer pour ce type d’événement comme suit.

Vous pouvez préparer à l’avance pour le basculement comme suit :

- [Préparer votre infrastructure et vos machines virtuelles](#plan-infrastructure-and-vm-connectivity) afin que vos charges de travail sera disponibles après le basculement, et afin que les utilisateurs peuvent accéder aux machines virtuelles Azure.
- Remarque la [basculement limites](#failover-limits) plus haut dans ce document. Assurez-vous que vos basculements tombent dans ces limites.
- Exécutez regular [exercices de récupération d’urgence](site-recovery-test-failover-to-azure.md). Exercices de vous aider à :
    - Rechercher des écarts dans votre déploiement avant le basculement.
    - Estimer le RTO de bout en bout pour vos applications.
    - Estimer le RPO de bout en bout pour vos charges de travail.
    - Identifier les conflits de plage d’adresses IP.
    - Lorsque vous exécutez des tests, nous recommandons que vous n’utiliser les réseaux de production pour les exercices, évitez d’utiliser les mêmes noms de sous-réseau dans les réseaux de production et de test et nettoyer les basculements de test après chaque extraction.

Pour exécuter un basculement à grande échelle, nous recommandons ce qui suit :

1. Créez des plans de récupération pour le basculement de la charge de travail.
    - Chaque plan de récupération peut déclencher le basculement des machines jusqu'à 50.
    - [Découvrez d’autres informations](recovery-plan-overview.md) sur les plans de récupération.
2. Ajouter des scripts de runbook Azure Automation à des plans de récupération, pour automatiser les tâches manuelles sur Azure. Les tâches typiques incluent la configuration d’équilibreurs de charge, etc. DNS de la mise à jour. [En savoir plus](site-recovery-runbook-automation.md)
2. Avant le basculement, préparez les machines Windows afin qu’ils se conforment à l’environnement Azure. [Limites de basculement](#plan-azure-subscriptions-and-quotas) sont plus élevées pour les ordinateurs qui sont conformes. [En savoir plus](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) sur les runbooks.
4.  Déclencher le basculement avec le [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) applet de commande PowerShell, ainsi que d’un plan de récupération.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Moniteur Site Recovery](site-recovery-monitor-and-troubleshoot.md)
