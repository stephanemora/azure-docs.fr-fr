---
title: Questions courantes sur VMware pour la récupération d’urgence Azure avec Azure Site Recovery | Microsoft Docs
description: Obtenez des réponses aux questions courantes sur la récupération d’urgence de machines virtuelles de VMware locales vers Azure à l’aide d’Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/10/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 4cc305e51113e67eb389bde8cb4cd28a3de8e3df
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969319"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Questions fréquentes sur VMware pour la réplication Azure

Cet article répond aux questions courantes qui peuvent s’afficher lorsque vous déployez la récupération d’urgence de machines virtuelles de VMware en local (machines virtuelles) vers Azure.

## <a name="general"></a>Généralités

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Qu’ai-je besoin pour la récupération d’urgence de VMware VM ?

[En savoir plus sur les composants impliqués](vmware-azure-architecture.md) dans la récupération d’urgence de machines virtuelles VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Puis-je utiliser Site Recovery pour migrer des machines virtuelles VMware vers Azure ?

Oui. Outre l’utilisation de Site Recovery comment configurer la récupération d’urgence complète pour les machines virtuelles VMware, vous pouvez également utiliser Site Recovery pour migrer des machines virtuelles VMware locales vers Azure. Dans ce scénario, vous répliquez des machines virtuelles VMware en local vers le stockage Azure. Ensuite, vous effectuez un basculement du site local vers Azure. Après le basculement, vos applications et charges de travail sont disponibles et s’exécutent sur les machines virtuelles Azure. Le processus est comme la configuration de la récupération d’urgence complète, à ceci près que dans une migration, vous ne pouvez pas revenir à partir d’Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Est-ce que mon compte Azure a besoin d’autorisations pour créer des machines virtuelles ?

Si vous êtes un administrateur d’abonnement, vous disposez des autorisations de réplication nécessaires. Si vous n’êtes pas un administrateur, vous avez besoin d’autorisations pour effectuer ces actions :

- Créer une machine virtuelle Azure dans la ressource de groupe et virtual network vous que vous spécifiez lorsque vous configurez Site Recovery.
- Écrire dans le compte de stockage sélectionné ou d’un disque géré en fonction de votre configuration.

[En savoir plus](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sur les autorisations requises.

### <a name="what-applications-can-i-replicate"></a>Quelles applications puis-je répliquer ?

Vous pouvez répliquer toute application ou la charge de travail en cours d’exécution sur une VM VMware qui répond à la [impératifs de réplication](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery prend en charge la réplication d’application, afin que les applications puissent être basculées et restauré sur un état intelligent.
- Site Recovery s’intègre avec les applications Microsoft telles que SharePoint, Exchange, Dynamics, SQL Server et Active Directory. Il travaille également en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat.

[En savoir plus](site-recovery-workload.md) sur la protection des charges de travail.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Puis-je utiliser une licence de serveur de système d’exploitation invité sur Azure ?

Oui, les clients de Microsoft Software Assurance peuvent utiliser [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) afin de réduire les coûts pour les ordinateurs Windows Server qui sont migrées vers Azure de licences ou d’utiliser Azure pour la récupération d’urgence.

## <a name="security"></a>Sécurité

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Quel est l’accès aux serveurs VMware Site Recovery a besoin ?

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Configurer une VM VMware exécutant le serveur de configuration de Site Recovery.
- Détecter automatiquement les machines virtuelles pour la réplication.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Accès aux machines virtuelles VMware Site Recovery a besoin ?

- Pour répliquer, une VM VMware doit avoir le service mobilité Site Recovery installé et en cours d’exécution. Vous pouvez déployer l’outil manuellement, ou vous pouvez spécifier que Site Recovery effectuer une installation push du service lorsque vous activez la réplication pour une machine virtuelle.
- Lors de la réplication, les machines virtuelles communiquent avec Site Recovery comme suit :
    - Les machines virtuelles communiquent avec le serveur de configuration sur le port HTTPS 443 pour la gestion de la réplication.
    - Machines virtuelles envoient des données de réplication au serveur de traitement sur le port HTTPS 9443. (Ce paramètre peut être modifié.)
    - Si vous activez la cohérence multimachine virtuelle, les machines virtuelles communiquent entre elles sur le port 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Est-ce que les données de réplication sont envoyées à Site Recovery ?

Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur ce qui s’exécute sur vos machines virtuelles. Les données de réplication sont échangées entre les hyperviseurs VMware et de stockage Azure. Site Recovery n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.  

Site Recovery est certifié pour ISO 27001 : 2013 et à 27018, HIPAA et DPA. Il est en train de SOC2 et JAB FedRAMP.

## <a name="pricing"></a>Tarifs

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Comment calculer les frais approximatives pour la récupération d’urgence de VMware ?

Utilisez le [calculatrice de prix](https://aka.ms/asr_pricing_calculator) pour estimer les coûts tout en utilisant Site Recovery.

Pour obtenir une estimation détaillée des coûts, exécutez l’outil deployment planner pour [VMware](https://aka.ms/siterecovery_deployment_planner) et utiliser le [rapport d’estimation des coûts](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Existe-t-il une différence de coût entre la réplication vers le stockage ou directement vers des disques gérés ?

Disques gérés sont facturés différemment à partir de comptes de stockage. [En savoir plus](https://azure.microsoft.com/pricing/details/managed-disks/) sur la tarification de disque géré.

## <a name="mobility-service"></a>Service Mobilité

### <a name="where-can-i-find-the-mobility-service-installers"></a>Où puis-je trouver les programmes d’installation du service Mobilité ?

Les programmes d’installation se trouvent dans le dossier %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository sur le serveur de configuration.

## <a name="how-do-i-install-the-mobility-service"></a>Comment installer le service Mobilité ?

Sur chaque machine virtuelle que vous souhaitez répliquer, installez le service en plusieurs méthodes :

- [Installation push](vmware-physical-mobility-service-overview.md#push-installation)
- [Installation manuelle](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) à partir de l’interface utilisateur ou de PowerShell
- Déploiement à l’aide d’un outil de déploiement tels que [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Disques managés

### <a name="where-does-site-recovery-replicate-data-to"></a>Où Site Recovery répliquer des données vers ?

Site Recovery réplique des machines virtuelles VMware en local et des serveurs physiques vers des disques gérés dans Azure.

- Le serveur de processus Site Recovery écrit des journaux de réplication dans un compte de stockage de cache dans la région cible.
- Ces journaux sont utilisés pour créer des points de récupération sur des disques gérés par Azure qui ont le préfixe de **asrseeddisk**.
- En cas de basculement, le point de récupération que vous sélectionnez est utilisé pour créer un nouveau disque géré cible. Ce disque managé est attaché à la machine virtuelle dans Azure.
- Machines virtuelles qui ont été précédemment répliqués vers un compte de stockage (avant mars 2019) ne sont pas affectés.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Puis-je répliquer des nouveaux ordinateurs aux comptes de stockage ?

Non. À compter de mars 2019, dans le portail Azure, vous pouvez répliquer uniquement sur Azure des disques gérés.

Réplication de nouvelles machines virtuelles à un compte de stockage est disponible uniquement à l’aide de PowerShell ou l’API REST (version 2018-01-10 ou 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quels sont les avantages de la réplication des disques gérés ?

[Découvrez comment](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery simplifie la récupération d’urgence avec des disques gérés.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Puis-je modifier le type de disque géré une fois qu’un ordinateur est protégé ?

Oui, vous pouvez facilement [modifier le type de disque géré](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) pour les réplications en cours. Avant de modifier le type, ne vérifiez qu’aucune signature d’accès partagé QU'URL est générée sur le disque géré :

1. Accédez à la **disque managé** ressource sur le portail Azure et vérifiez si vous avez une bannière d’URL de signature accès partagé le **vue d’ensemble** panneau.
1. Si la bannière est présente, sélectionnez-le pour annuler l’exportation en cours.
1. Modifier le type du disque dans quelques minutes. Si vous modifiez le type de disque managé, attendez que les points de récupération fraîches devant être généré par Azure Site Recovery.
1. Utilisez les nouveaux points de récupération pour n’importe quel test de basculement ou le basculement à l’avenir.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Puis-je basculer la réplication à partir de disques gérés pour les disques non gérés ?

Non. Passage de géré à non géré n’est pas pris en charge.

## <a name="replication"></a>Réplication

### <a name="what-are-the-replicated-vm-requirements"></a>Quelle est la configuration requise d’une machine virtuelle répliquée ?

[En savoir plus](vmware-physical-azure-support-matrix.md#replicated-machines) sur les exigences de prise en charge pour les machines virtuelles VMware et des serveurs physiques.

### <a name="how-often-can-i-replicate-to-azure"></a>À quelle fréquence puis-je répliquer vers Azure ?

La réplication est continue quand il s’agit de la réplication des machines virtuelles VMware vers Azure.

### <a name="can-i-extend-replication"></a>Puis-je étendre la réplication ?

La réplication étendue ou chaînée n’est pas prise en charge. Demandez cette fonctionnalité dans le [forum de commentaires](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Puis-je effectuer une réplication initiale hors connexion ?

Réplication hors connexion n’est pas pris en charge. Demandez cette fonctionnalité dans le [forum de commentaires](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Qu’est asrseeddisk ?

Pour chaque disque source, les données sont répliquées sur un disque géré dans Azure. Ce disque a le préfixe **asrseeddisk**. Il stocke la copie du disque source et de tous les instantanés de point de récupération.

### <a name="can-i-exclude-disks-from-replication"></a>Puis-je exclure des disques de la réplication ?

Oui, vous pouvez exclure des disques.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Puis-je répliquer des machines virtuelles dotées de disques dynamiques ?

Les disques dynamiques peuvent être répliqués. Le disque du système d’exploitation doit être un disque de base.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Si j’utilise des groupes de réplication pour la cohérence entre plusieurs machines virtuelles, puis-je ajouter une nouvelle machine virtuelle à un groupe de réplication existant ?

Oui, vous pouvez ajouter de nouvelles machines virtuelles à un groupe de réplication existant lorsque vous activez la réplication. Toutefois :

- Vous ne pouvez pas ajouter une machine virtuelle à un groupe de réplication existant après que la réplication a commencé.
- Impossible de créer un groupe de réplication pour les machines virtuelles existantes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Puis-je modifier les machines virtuelles répliquées via l’ajout ou le redimensionnement de disques ?

Pour la réplication de VMware vers Azure, vous pouvez modifier la taille du disque. Si vous souhaitez ajouter de nouveaux disques, vous devez ajouter le disque et réactivez la protection de la machine virtuelle.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Puis-je migrer les machines locales vers un nouveau serveur vCenter Server sans affecter la réplication en cours ?

Non. Une modification de VMware Vcenter ou la migration aura un impact sur la réplication en cours. Configurer la récupération de Site avec le nouveau serveur vCenter Server et réactivez la réplication pour les machines.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Puis-je répliquer vers un compte de stockage de cache ou de cible qui dispose d’un réseau virtuel (avec les pare-feux Azure) configuré sur ce dernier ?

Non, Site Recovery ne prend en charge la réplication vers le stockage Azure sur les réseaux virtuels.

## <a name="component-upgrade"></a>Mise à niveau du composant

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Ma version du serveur de configuration ou de l’agent de services Mobility est ancienne et ma mise à niveau a échoué. Que faire ?

Site Recovery suit le modèle de prise en charge de N-4. [En savoir plus](https://aka.ms/asr_support_statement) sur la mise à niveau à partir des versions très anciennes.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Où puis-je trouver les notes de publication et correctifs cumulatifs pour Azure Site Recovery ?

[En savoir plus sur les nouvelles mises à jour](site-recovery-whats-new.md), et [obtenir des informations sur le cumul](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Où puis-je trouver des informations de mise à niveau pour la récupération d’urgence vers Azure ?

[En savoir plus sur la mise à niveau](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Ai-je besoin de redémarrer les machines sources pour chaque mise à niveau ?

Un redémarrage est recommandé mais pas obligatoire pour chaque mise à niveau. [Plus d’informations](https://aka.ms/asr_vmware_upgrades)

## <a name="configuration-server"></a>Serveur de configuration

### <a name="what-does-the-configuration-server-do"></a>Quel fait le serveur de configuration ?

Le serveur de configuration exécute les composants Site Recovery en local, notamment :

- Le serveur de configuration lui-même. Le serveur coordonne la communication entre les composants locaux et Azure et gère la réplication des données.
- Le serveur de processus, qui sert de passerelle de réplication. Ce serveur :
    1. Reçoit les données de réplication.
    2. Optimise les données à l’aide de la mise en cache, la compression et le chiffrement.
    3. Envoie les données vers le stockage Azure.
  Également, le serveur de processus est une installation push du Service mobilité sur des machines virtuelles et effectue la détection automatique des machines virtuelles VMware en local.
- Le serveur cible maître, qui gère les données de réplication pendant la restauration automatique à partir d’Azure.

[Découvrez-en plus](vmware-azure-architecture.md) sur les composants et les processus du serveur de configuration.

### <a name="where-do-i-set-up-the-configuration-server"></a>Où configurer le serveur de configuration ?

Vous avez besoin d’une machine virtuelle de VMware unique, hautement disponible en local pour le serveur de configuration. Pour la récupération d’urgence de serveur physique, installez le serveur de configuration sur un ordinateur physique.

### <a name="what-do-i-need-for-the-configuration-server"></a>Qu’ai-je besoin pour le serveur de configuration ?

Examinez les [conditions préalables](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Puis-je configurer manuellement le serveur de configuration au lieu d’utiliser un modèle ?

Il est recommandé que vous [créer la machine virtuelle du serveur de configuration](vmware-azure-deploy-configuration-server.md) à l’aide de la dernière version du modèle de Machine virtuelle Format OVF (Open). Si vous ne pouvez pas utiliser le modèle (par exemple, si vous n’avez pas accès au serveur VMware), [télécharger](physical-azure-set-up-source.md) le fichier d’installation à partir du portail et configurer le serveur de configuration.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un serveur de configuration peut-il répliquer vers plusieurs régions ?

Non. Pour répliquer dans plusieurs régions, vous avez besoin d’un serveur de configuration dans chaque région.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Puis-je héberger un serveur de configuration dans Azure ?

Bien qu’il soit possible, la machine virtuelle Azure exécutant le serveur de configuration doit communiquer avec votre infrastructure de VMware en local et les machines virtuelles. Cette communication ajoute une latence et a un impact sur la réplication en cours.

### <a name="how-do-i-update-the-configuration-server"></a>Comment mettre à jour le serveur de configuration ?

[En savoir plus](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) comment mettre à jour le serveur de configuration.

- Vous trouverez les dernières informations de mise à jour sur le [page mises à jour Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Vous pouvez télécharger la dernière version à partir du portail. Ou, vous pouvez télécharger la dernière version du serveur de configuration directement à partir de la [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Si votre version est antérieure à la version actuelle de plus de quatre versions, consultez le [prend en charge instruction](https://aka.ms/asr_support_statement) pour obtenir des conseils de mise à niveau.

### <a name="should-i-back-up-the-configuration-server"></a>Dois-je sauvegarder le serveur de configuration ?

Nous vous recommandons d’effectuer des sauvegardes planifiées régulières du serveur de configuration.

- Pour la restauration automatique réussie, la machine virtuelle en cours restaurée doit exister dans la base de données du serveur de configuration.
- Le serveur de configuration doit être en cours d’exécution et en état connecté.
- [En savoir plus](vmware-azure-manage-configuration-server.md) sur les tâches de gestion de serveur de configuration courantes.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Lorsque je configure le serveur de configuration, puis-je télécharger et installer MySQL manuellement ?

Oui. Télécharger MySQL et placez-le dans le dossier C:\Temp\ASRSetup. Ensuite, installez-le manuellement. Lorsque vous configurez la machine virtuelle du serveur de configuration et acceptez les termes du contrat, MySQL est répertorié comme **Déjà installé** dans **Télécharger et installer**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Puis-je ne pas télécharger MySQL et laisser Site Recovery l’installer ?

Oui. Télécharger le programme d’installation de MySQL et placez-le dans le dossier C:\Temp\ASRSetup. Lorsque vous configurez la machine virtuelle du serveur de configuration, acceptez les termes du contrat, puis sélectionnez **Téléchargez et installez**. Le portail utilise le programme d’installation que vous avez ajouté pour installer MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Puis-je utiliser la machine virtuelle du serveur de configuration à d’autres fins ?

Non. Utilisez la machine virtuelle uniquement pour le serveur de configuration.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Puis-je cloner un serveur de configuration et l’utiliser pour l’orchestration ?

Non. Configurer un serveur de la nouvelle configuration pour éviter les problèmes d’inscription.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Puis-je modifier le coffre dans lequel le serveur de configuration est inscrit ?

Non. Une fois un coffre est associé avec le serveur de configuration, il ne peut pas être modifié. [En savoir plus](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) sur l’inscription d’un serveur de configuration avec un autre coffre.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Puis-je utiliser le même serveur de configuration pour la récupération d’urgence de machines virtuelles VMware et des serveurs physiques ?

Oui, mais notez l’échec de l’ordinateur physique automatique uniquement vers une VM VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Où puis-je télécharger la phrase secrète du serveur de configuration ?

[En savoir plus](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) comment télécharger la phrase secrète.

### <a name="where-can-i-download-vault-registration-keys"></a>Où puis-je télécharger les clés d’inscription du coffre ?

Dans le coffre Recovery Services, sélectionnez **serveurs de Configuration** dans **Infrastructure Site Recovery** > **gérer**. Ensuite, dans **serveurs**, sélectionnez **télécharger une clé d’inscription** pour télécharger le fichier d’informations d’identification de coffre.

## <a name="process-server"></a>Serveur de traitement

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Pourquoi ne puis-je pas sélectionner le serveur de processus lorsque j’ai activez la réplication ?

Mises à jour dans les versions 9,24 et ultérieures s’affichent désormais le [l’intégrité du serveur de processus lorsque vous activez la réplication](vmware-azure-enable-replication.md#enable-replication). Cette fonctionnalité permet d’éviter la limitation du serveur de processus et réduire l’utilisation de serveurs de traitement défectueux.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Comment mettre à jour le serveur de processus vers la version 9.24 ou version ultérieure pour les informations d’intégrité précises ?

À partir de [version 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), davantage d’alertes ont été ajoutés pour indiquer l’intégrité du serveur de processus. [Mise à jour de vos composants de Site Recovery pour 9.24 ou version ultérieure] (service-updates-how-to.md#links-to-currently-supported-update-rollups) afin que toutes les alertes sont générées.

## <a name="failover-and-failback"></a>Basculement et restauration automatique

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Puis-je utiliser le serveur de processus en local pour la restauration automatique ?

Nous vous recommandons vivement de créer un serveur de processus dans Azure à des fins de restauration automatique, afin d’éviter les latences de transfert de données. En outre, au cas où vous séparés du réseau de machines virtuelles source avec le réseau accessible sur Azure dans le serveur de configuration, il est essentiel d’utiliser le serveur de processus créé dans Azure pour la restauration automatique.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Puis-je conserver l’adresse IP de basculement ?

Oui, vous pouvez conserver l’adresse IP lors du basculement. Veillez à spécifier l’adresse IP cible dans le **calcul et réseau** paramètres pour la machine virtuelle avant le basculement. En outre, arrêter les machines au moment du basculement afin d’éviter les conflits d’adresses IP pendant la restauration automatique.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Puis-je modifier la taille de machine virtuelle cible ou le type de machine virtuelle avant le basculement ?

Oui, vous pouvez modifier le type ou la taille de la machine virtuelle à tout moment avant le basculement. Dans le portail, vous devez utiliser le **calcul et réseau** paramètres pour la machine virtuelle répliquée.

### <a name="how-far-back-can-i-recover"></a>Jusqu’à quand peut remonter la récupération ?

Pour VMware vers Azure, le point de récupération plus ancien, que vous pouvez utiliser est de 72 heures.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Comment accéder aux machines virtuelles Azure après un basculement ?

Après le basculement, vous pouvez accéder des machines virtuelles Azure via une connexion internet sécurisée, via un VPN de site à site ou via Azure ExpressRoute. Pour vous connecter, vous devez préparer plusieurs choses. [Plus d’informations](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Résiste les données ayant basculé ?

Azure est conçu pour la résilience. Site Recovery est conçu pour le basculement vers Azure centres de données secondaires, comme requis par le contrat de niveau de service (SLA) Azure. En cas de basculement, nous nous assurons que vos métadonnées et vos coffres restent dans la même région géographique que vous avez choisi pour votre coffre.

### <a name="is-failover-automatic"></a>Le basculement est-il automatique ?

Le [basculement](site-recovery-failover.md) n’est pas automatique. Vous démarrez un basculement en effectuant une sélection unique dans le portail, ou vous pouvez utiliser [PowerShell](/powershell/module/az.recoveryservices) pour déclencher un basculement.

### <a name="can-i-fail-back-to-a-different-location"></a>Puis-je effectuer la restauration à un autre emplacement ?

Oui. Si vous avez basculé vers Azure, vous pouvez effectuer à un autre emplacement si celui d’origine n’est pas disponible. [Plus d’informations](concepts-types-of-failback.md#alternate-location-recovery-alr)

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Pourquoi une connexion VPN ou ExpressRoute avec l’homologation privée pour la restauration automatique ?

Lorsque vous procédez à partir d’Azure, les données à partir d’Azure sont copiées vers votre machine virtuelle en local et un accès privé est requis.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Puis-je redimensionner la machine virtuelle Azure après basculement ?

Non, vous ne pouvez pas modifier la taille ou le type de la machine virtuelle cible après le basculement.

## <a name="automation-and-scripting"></a>Automatisation et scripts

### <a name="can-i-set-up-replication-with-scripting"></a>Puis-je configurer la réplication avec des scripts ?

Oui. Vous pouvez automatiser les flux de travail Site Recovery à l’aide de l’API Rest, PowerShell ou le Kit de développement. [Plus d’informations](vmware-azure-disaster-recovery-powershell.md)

## <a name="performance-and-capacity"></a>Performances et capacité

### <a name="can-i-throttle-replication-bandwidth"></a>Est-il possible de limiter la bande passante de réplication ?

Oui. [Plus d’informations](site-recovery-plan-capacity-vmware.md)

## <a name="next-steps"></a>Étapes suivantes

- [Examiner](vmware-physical-azure-support-matrix.md) les conditions de prise en charge.
- [Configurer](vmware-azure-tutorial.md) la réplication VMware vers Azure.
