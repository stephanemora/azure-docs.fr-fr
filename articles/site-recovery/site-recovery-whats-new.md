---
title: Nouveautés d’Azure Site Recovery | Microsoft Docs
description: Fournit un récapitulatif des nouvelles fonctionnalités introduites dans Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: 1ce71665a924c6681af418075d8cc635c73f8a1a
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717673"
---
# <a name="whats-new-in-site-recovery"></a>Nouveautés de Site Recovery

Le service [Azure Site Recovery](site-recovery-overview.md) fait l’objet de mises à jour et d’améliorations continues. Pour vous aider à rester à jour, cet article vous donne des informations sur les versions les plus récentes, les nouvelles fonctionnalités et le nouveau contenu. Cette page est régulièrement mise à jour.

Si vous avez des suggestions concernant les fonctionnalités de Site Recovery, [faites-nous part de vos commentaires](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="installing-updates"></a>Installation des mises à jour

[Consultez cet article](service-updates-how-to.md) pour obtenir des instructions et du support sur la mise à jour de Site Recovery.

## <a name="updates-july-2019"></a>Mises à jour (juillet 2019)

### <a name="update-rollup-38"></a>Correctif cumulatif 38

Le [Correctif cumulatif 38](https://support.microsoft.com/help/4513507/) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Mises à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif)
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif)


### <a name="general"></a>Généralités

Site Recovery prend désormais en charge l’utilisation de comptes de stockage universel v2 pour le stockage de cache ou le stockage cible. Auparavant, seul v1 était pris en charge.

### <a name="vmware-to-azure-disaster-recovery"></a>Récupération d’urgence VMware vers Azure

Vous pouvez désormais répliquer des disques jusqu’à 8 To lors de la réplication d’une machine virtuelle Azure avec des disques managés.


## <a name="updates-june-2019"></a>Mises à jour (juin 2019)

### <a name="update-rollup-37"></a>Correctif cumulatif 37

Le [Correctif cumulatif 37](https://support.microsoft.com/help/4508614/) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Mises à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif)
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/récupération d’urgence de serveur physique

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Partitions GPT** | À partir du correctif cumulatif 37 (version du service mobilité 9.25.5241.1), jusqu’à cinq partitions GPT sont prises en charge en mode UEFI. Avant cette mise à jour, seules quatre étaient prises en charge.



## <a name="updates-may-2019"></a>Mises à jour (mai 2019)

### <a name="update-rollup-36"></a>Correctif cumulatif 36

Le [Correctif cumulatif 36](https://support.microsoft.com/help/4503156) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif)
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif)

### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machine virtuelle Azure

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Répliquer les disques ajoutés** | Permet la réplication pour les disques de données ajoutés à une machine virtuelle Azure déjà activée pour la récupération d’urgence. [Plus d’informations](azure-to-azure-enable-replication-added-disk.md)
**Mises à jour automatiques** | Lors de la configuration des mises à jour automatiques de l’extension du service de mobilité qui s’exécute sur des machines virtuelles Azure activées pour la récupération d’urgence, vous pouvez maintenant sélectionner un compte Automation existant au lieu d’utiliser le compte par défaut créé par Site Recovery. [Plus d’informations](azure-to-azure-autoupdate.md)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/récupération d’urgence de serveur physique

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Surveillance des serveurs de traitement** | Pour la récupération d’urgence des machines virtuelles VMware et des serveurs physiques locaux, surveillez et résolvez les problèmes liés aux serveurs de traitement grâce à des alertes et rapports améliorés sur l’intégrité des serveurs. [Plus d’informations](vmware-physical-azure-monitor-process-server.md) 





## <a name="updates-march-2019"></a>Mises à jour (mars 2019)

### <a name="update-rollup-35"></a>Correctif cumulatif 35

Le [Correctif cumulatif 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif)
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/récupération d’urgence de serveur physique

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Disques managés** | La réplication de machines virtuelles locales VMware et de serveurs physiques s’effectue désormais directement sur les disques managés dans Azure. Les données locales sont envoyées vers un compte de stockage de cache dans Azure et les points de récupération sont créés sur des disques managés à l’emplacement cible. Ainsi, vous n’avez pas besoin de gérer plusieurs comptes de stockage cible.
**Serveur de configuration** | Site Recovery prend désormais en charge les serveurs de configuration avec plusieurs cartes réseau. Ajoutez des adaptateurs supplémentaires sur la machine virtuelle du serveur de configuration avant d’inscrire ce dernier dans le coffre. Ensuite, vous devez réinscrire le serveur dans le coffre.


## <a name="updates-february-2019"></a>Mises à jour (février 2019)

### <a name="update-rollup-34"></a>Correctif cumulatif 34 

Le [Correctif cumulatif 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).


### <a name="update-rollup-33"></a>Correctif cumulatif 33 

Le [Correctif cumulatif 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).


### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machine virtuelle Azure 
Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Mappage réseau** | Pour la récupération d’urgence de machines virtuelles Azure, vous pouvez maintenant utiliser n’importe quel réseau cible disponible lorsque vous activez la réplication. 
**SSD Standard** | Vous pouvez désormais configurer la récupération d’urgence pour les machines virtuelles Azure utilisant des [disques SSD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaces de stockage direct** | Vous pouvez configurer la récupération d’urgence pour les applis s’exécutant sur les applications de machine virtuelle Azure à l’aide de [Storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pour une haute disponibilité.  L’utilisation de Storage Spaces Direct (S2D) avec Site Recovery offre une protection complète aux charges de travail de machine virtuelle Azure. S2D vous permet d’héberger un cluster invité dans Azure. Cela est particulièrement utile lorsqu’une machine virtuelle héberge une application critique, comme la couche SAP ASCS, SQL Server ou un serveur de fichiers avec montée en puissance parallèle.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/récupération d’urgence de serveur physique
Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Système de fichiers Linux BTRFS** | Site Recovery prend désormais en charge la réplication des machines virtuelles VMware avec le système de fichiers BTRFS. La réplication n’est pas prise en charge dans les cas suivants :<br/><br/>- Le sous-volume du système de fichiers BTRFS est modifié après l’activation de la réplication.<br/><br/>- Le système de fichiers est réparti sur plusieurs disques.<br/><br/>- Le système de fichiers BTRFS prend en charge RAID.
**Windows Server 2019** | Prise en charge ajoutée pour les machines exécutant Windows Server 2019.


## <a name="updates-january-2019"></a>Mises à jour (janvier 2019)


### <a name="accelerated-networking-azure-vms"></a>Mise en réseau accélérée (machines virtuelles Azure)

L’accélération réseau permet la virtualisation d’E/S de racine unique (SR-IOV) sur une machine virtuelle, améliorant ainsi les performances réseau. Quand vous activez la réplication pour une machine virtuelle Azure, Site Recovery détecte si l’accélération réseau est activée ou non. Si elle l’est, Site Recovery configure automatiquement l’accélération réseau sur la machine virtuelle Azure du réplica cible après le basculement, à la fois sur [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) et sur [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Plus d’informations](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="update-rollup-32"></a>Correctif cumulatif 32 

Le [Correctif cumulatif 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).

### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machine virtuelle Azure

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge de RedHat Workstation 6/7, et nouvelles versions de noyau pour Ubuntu, Debian et SUSE.
**Espaces de stockage direct** | Site Recovery prend en charge les machines virtuelles Azure utilisant Storage Spaces Direct (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Récupération d’urgence des machines virtuelles VMware/serveurs physiques

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.
 
**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge de RedHat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10/7.6, et nouvelles versions de noyau pour Ubuntu, Debian et SUSE.


### <a name="update-rollup-31"></a>Correctif cumulatif 31 

Le [Correctif cumulatif 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).

### <a name="vmware-vmsphysical-servers-replication"></a>Réplication des machines virtuelles VMware/serveurs physiques 
Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.
**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge d’Oracle Linux 6.8 et 6.9/7.0, et pour le noyau UEK5.
**LVM** | Prend désormais en charge les volumes LVM et LVM2.<br/><br/> Le répertoire /boot des partitions de disque et des volumes LVM est maintenant pris en charge.
**Directories** | Prend désormais en charge les répertoires configurés en tant que partitions distinctes, ou les systèmes de fichiers qui ne se trouvent pas sur le même disque système :<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Prend désormais en charge les disques dynamiques.
**Type de basculement** | Amélioration des temps de basculement pour les machines virtuelles VMware lorsque storvsc et vsbus ne sont pas des pilotes de démarrage.
**Prise en charge du mode UEFI** | Les machines virtuelles Azure ne prennent pas en charge le mode de démarrage UEFI. Vous pouvez désormais migrer les serveurs physiques locaux avec UEFI vers Azure à l’aide de Site Recovery Site Recovery migre le serveur en convertissant le mode de démarrage en BIOS avant la migration. Site Recovery prenait auparavant en charge cette conversion pour les machines virtuelles uniquement. Les serveurs physiques exécutant Windows Server 2012 ou version ultérieure sont pris en charge.

### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machine virtuelle Azure
Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge d’Oracle Linux 6.8 et 6.9/7.0, et du noyau UEK5.
**Système de fichiers Linux BTRFS** | Pris en charge pour les machines virtuelles Azure.
**Machines virtuelles Azure dans les zones de disponibilité** | Vous pouvez activer la réplication vers une autre région pour les machines virtuelles Azure déployées dans les zones de disponibilité. Vous pouvez désormais activer la réplication pour une machine virtuelle Azure et définir comme cible du basculement une instance de machine virtuelle unique, une machine virtuelle dans un groupe à haute disponibilité ou une machine virtuelle dans une zone de disponibilité. Le paramètre n’impacte pas la réplication. [Lisez](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l’annonce.
**Stockage avec pare-feu (portail/PowerShell)** | Nouvelle prise en charge des [comptes de stockage avec pare-feu](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Vous pouvez répliquer des machines virtuelles Azure avec des disques non managés sur des comptes de stockage avec pare-feu sur une autre région Azure pour les scénarios de récupération d’urgence.<br/><br/> Vous pouvez utiliser des comptes de stockage avec pare-feu en tant que comptes de stockage cibles pour des disques non managés.<br/><br/> Fonction prise en charge dans le portail et avec PowerShell.

## <a name="updates-december-2018"></a>Mises à jour (décembre 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Mises à jour automatiques du service de mobilité (machines virtuelles Azure)

Site Recovery propose désormais une option pour mettre à jour automatiquement l’extension du service Mobilité. L’extension du service Mobilité est installée sur chaque machine virtuelle Azure répliquée par Site Recovery. Quand vous activez la réplication, vous pouvez autoriser Site Recovery à gérer les mises à jour de l’extension.

Les mises à jour ne nécessitent pas le redémarrage de la machine virtuelle et n’affectent pas la réplication. [Plus d’informations](azure-to-azure-autoupdate.md)

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculatrice de prix pour la reprise d’activité de machines virtuelles Azure

La récupération d’urgence des machines virtuelles Azure occasionne des coûts liés aux licences des machines virtuelles ainsi que des frais de stockage et de réseau. Azure fournit une [calculatrice de prix](https://aka.ms/a2a-cost-estimator) pour vous aider à déterminer ces coûts. Site Recovery propose désormais un [exemple d’estimation du prix](https://aka.ms/a2a-cost-estimator) d’un déploiement basé sur une application à trois niveaux comprenant six machines virtuelles avec 12 disques HDD Standard et 6 disques SSD Premium.

- L’exemple suppose des changements de données totalisant 10 Go par jour pour le stockage Standard et 20 Go pour le stockage Premium.
- Pour estimer les coûts de votre propre déploiement, changez les variables.
- Vous pouvez spécifier le nombre de machines virtuelles, le nombre et le type de disques managés ainsi que le taux de changement de données total attendu sur les machines virtuelles.
- Vous pouvez également appliquer un facteur de compression pour estimer les coûts de bande passante.

[Lisez](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l’annonce.


## <a name="updates-october-2018"></a>Mises à jour (octobre 2018)

### <a name="update-rollup-30"></a>Correctif cumulatif 30 

Le [Correctif cumulatif 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).

### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machine virtuelle Azure
Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge des régions** | Site Recovery prend désormais en charge les régions Australie Centre 1 et Australie Centre 2.
**Prise en charge du chiffrement de disque** | Prise en charge ajoutée pour la récupération d’urgence des machines virtuelles Azure chiffrées au moyen d’ADE (Azure Disk Encryption) avec l’application Azure AD. [Plus d’informations](azure-to-azure-how-to-enable-replication-ade-vms.md)
**Exclusion de disque** | Les disques non initialisés sont maintenant automatiquement exclus lors de la réplication de machine virtuelle Azure.
**Stockage avec pare-feu (PowerShell)** | Nouvelle prise en charge des [comptes de stockage avec pare-feu](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Vous pouvez répliquer des machines virtuelles Azure avec des disques non managés sur des comptes de stockage avec pare-feu sur une autre région Azure pour les scénarios de récupération d’urgence.<br/><br/> Vous pouvez utiliser des comptes de stockage avec pare-feu en tant que comptes de stockage cibles pour des disques non managés.<br/><br/> Prise en charge avec PowerShell uniquement.


### <a name="update-rollup-29"></a>Correctif cumulatif 29 

Le [Correctif cumulatif 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).


## <a name="updates-august-2018"></a>Mises à jour (août 2018)

### <a name="update-rollup-28"></a>Correctif cumulatif 28 

Le [Correctif cumulatif 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).

### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machine virtuelle Azure 
Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge de RedHat Enterprise Linux 6.10 ; CentOS 6.10.<br/><br/>
**Prise en charge du cloud** | Prise en charge de la récupération d’urgence pour les machines virtuelles Azure dans le cloud allemand.
**Récupération d’urgence entre abonnements** | Prise en charge de la réplication des machines virtuelles Azure d’une région vers une autre, dans un autre abonnement du même locataire Azure Active Directory. [Plus d’informations](https://aka.ms/cross-sub-blog)

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Récupération d’urgence des machines virtuelles VMware/serveurs physiques 
Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge de RedHat Enterprise Linux 6.10 ; CentOS 6.10.<br/><br/> Les machines virtuelles Linux utilisant le style de partition GPT (GUID Partition Table) en mode de compatibilité avec un BIOS hérité sont désormais prises en charge. Consultez le [FAQ sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) pour plus d’informations. 
**Récupération d’urgence des machines virtuelles après la migration** | Prise en charge de la récupération d’urgence vers une région secondaire pour une machine virtuelle VMware locale migrée vers Azure, sans avoir à désinstaller le service de mobilité sur la machine virtuelle avant d’activer la réplication.
**Windows Server 2008** | Prise en charge de la migration des machines exécutant Windows Server 2008 R2/2008 64 bits et 32 bits.<br/><br/> Migration uniquement (réplication et basculement). La restauration automatique n’est pas prise en charge.

## <a name="updates-july-2018"></a>Mises à jour (juillet 2018)

### <a name="update-rollup-27-july-2018"></a>Correctif cumulatif 27 (juillet 2018)

Le [Correctif cumulatif 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fournit les mises à jour suivantes.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents et des fournisseurs Site Recovery (comme décrit dans le correctif cumulatif).
**Correctifs/améliorations** | Un certain nombre de correctifs et d’améliorations (comme décrit dans le correctif cumulatif).

### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machine virtuelle Azure 

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge de Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Récupération d’urgence des machines virtuelles VMware/serveurs physiques 

Les fonctionnalités ajoutées ce mois-ci sont résumées dans le tableau.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Nouvelle prise en charge de RedHat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Étapes suivantes

Pour vous tenir informé de nos mises à jour, consultez la page [Mises à jour Azure](https://azure.microsoft.com/updates/?product=site-recovery).
