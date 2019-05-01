---
title: Nouveautés d’Azure Site Recovery | Microsoft Docs
description: Fournit un récapitulatif des nouvelles fonctionnalités introduites dans Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: 5ee1328dddb6ae1e1c878384097b0e10aa32feeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776286"
---
# <a name="whats-new-in-site-recovery"></a>Nouveautés de Site Recovery

Le service [Azure Site Recovery](site-recovery-overview.md) fait l’objet de mises à jour et d’améliorations continues. Pour vous aider à rester à jour, cet article vous donne des informations sur les versions les plus récentes, les nouvelles fonctionnalités et le nouveau contenu. Cette page est régulièrement mise à jour.

Si vous avez des suggestions concernant les fonctionnalités de Site Recovery, [faites-nous part de vos commentaires](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Premier trimestre 2019 

### <a name="update-rollup-34-february-2019"></a>Mise à jour cumulative 34 (février 2019)

[Correctif cumulatif 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Correctifs de problème** | Un nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif)



### <a name="update-rollup-33-february-2019"></a>Mise à jour cumulative 33 (février 2019)

[Correctif cumulatif 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Correctifs de problème** | Un nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif)
**Mappage réseau** | Pour la récupération d’urgence de machines virtuelles Azure, vous pouvez maintenant utiliser n’importe quel réseau cible disponible lorsque vous activez la réplication. 
**Disque SSD standard** | Vous pouvez maintenant définir la récupération d’urgence pour les machines virtuelles Azure à l’aide [disques SSD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaces de stockage Direct** | Vous pouvez configurer la récupération d’urgence pour les applications qui s’exécutent sur les applications de machine virtuelle Azure à l’aide de [espaces de stockage Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pour la haute disponibilité.
**Système de fichiers BRTFS** | Prise en charge pour les machines virtuelles VMware, en plus des machines virtuelles Azure.<br/><br/> Non pris en charge si : Le sous-volume de système de fichiers BTRFS est modifié après l’activation de la réplication, le système de fichiers est réparti sur plusieurs disques, ou si les BTRFS système de fichiers prend en charge RAID.



### <a name="update-rollup-32-january-2019"></a>Mise à jour cumulative 32 (janvier 2019)

[Correctif cumulatif 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Correctifs de problème** | Un nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif)
**Récupération d’urgence pour Linux** | **Machines virtuelles Azure** : Station de travail Red Hat 6/7 ; prise en charge pour les nouvelles versions du noyau Ubuntu, Debian et SUSE.<br/><br/> **Serveurs de machines virtuelles VMware/physique**: Red Hat Enterprise Linux 7.6 ; Station de travail Red Hat 6/7 ; Oracle Linux 6.10/7.6 ; prendre en charge pour les nouvelles versions de noyau pour Ubuntu, Debian et SUSE.


### <a name="update-rollup-31-january-2019"></a>Mise à jour cumulative 31 (janvier 2019)

[Correctif cumulatif 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Correctifs de problème** | Un nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif)
**Récupération d’urgence pour Linux** | **Machines virtuelles Azure** : Oracle Linux 6.8 et 6.9/7.0 ; prise en charge pour les noyaux UEK5.<br/><br/> **Serveurs de machines virtuelles VMware/physique**: Oracle Linux 6.8 et 6.9/7.0 ; prise en charge pour le noyau de UEK5.
**Système de fichiers BRTFS** | Prise en charge pour les machines virtuelles Azure.
**LVM** | Prend désormais en charge des volumes LVM et LVM2.<br/><br/> Le répertoire /boot sur une partition de disque et sur des volumes LVM est pris en charge.
**Directories** | Prise en charge ajoutée pour ces seet répertoires en tant que partitions distinctes ou des systèmes de fichiers qui ne sont pas sur le même disque de système : / (racine), /boot, / usr, / usr/local, / var, /etc.
**Windows Server 2008** | Prend désormais en charge les disques dynamiques.
**Basculement de VMware VM** | Amélioration des temps de basculement où storvsc et vsbus ne sont pas des pilotes de démarrage des machines virtuelles VMware.
**Prise en charge UEFI** | Les machines virtuelles Azure ne prennent pas en charge le type de démarrage UEFI. Vous pouvez désormais migrer des serveurs physiques locaux avec UEFI vers Azure avec Site Recovery. Site Recovery migre le serveur en convertissant le type de démarrage en BIOS avant la migration. Site Recovery précédemment pris en charge cette conversion pour les machines virtuelles uniquement. Prise en charge est disponible pour les serveurs physiques exécutant Windows Server 2012 ou version ultérieure.
**Machines virtuelles Azure dans les zones de disponibilité** | Vous pouvez activer la réplication vers une autre région pour machines virtuelles Azure déployées dans les zones de disponibilité. unité d’organisation permettre maintenant activer la réplication pour une machine virtuelle Azure et définissez la cible pour le basculement vers une seule instance de machine virtuelle, une machine virtuelle dans un groupe à haute disponibilité, une machine virtuelle ou dans une zone de disponibilité. Le paramètre n’impacte pas la réplication. [Lisez](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l’annonce.


## <a name="q4-2018"></a>Quatrième trimestre 2018

### <a name="update-rollup-30-october-2018"></a>Mise à jour cumulative 30 (octobre 2018)

[Correctif cumulatif 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Correctifs de problème** | Un nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif)
**Prise en charge de la région** | Site Recovery prise en charge de 1 Central de l’Australie et en Australie centrale 2.
**Prise en charge pour le chiffrement de disque** | Prise en charge ajoutée pour la récupération d’urgence de machines virtuelles Azure chiffrées avec Azure Disk Encryption (ADE) avec l’application Azure AD. [Plus d’informations](azure-to-azure-how-to-enable-replication-ade-vms.md)
**Exclusion de disque** | Disques non initialisées sont désormais automatiquement exclus lors de la réplication de machine virtuelle Azure.
**Stockage pare-feu activé** | Prise en charge ajoutée pour [comptes de stockage pare-feu activé](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Vous pouvez répliquer des machines virtuelles Azure avec disques non gérés sur les comptes de stockage pare-feu activé sur une autre région Azure de récupération d’urgence.<br/><br/> Vous pouvez utiliser des comptes de stockage pare-feu activé en tant que comptes de stockage cible pour les disques non gérés.<br/><br/> Prise en charge à l’aide de PowerShell uniquement.


### <a name="update-rollup-29-october-2018"></a>Mise à jour cumulative 29 (octobre 2018)

[Correctif cumulatif 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Correctifs de problème** | Un nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Mises à jour automatiques de l’extension du service Mobilité

Site Recovery propose désormais une option pour mettre à jour automatiquement l’extension du service Mobilité. L’extension du service Mobilité est installée sur chaque machine virtuelle Azure répliquée par Site Recovery. Quand vous activez la réplication, vous pouvez autoriser Site Recovery à gérer les mises à jour de l’extension. Les mises à jour ne nécessitent pas le redémarrage de la machine virtuelle et n’affectent pas la réplication. [Plus d’informations](azure-to-azure-autoupdate.md)

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Reprise d’activité pour les machines virtuelles utilisant l’accélération réseau

L’accélération réseau permet d’opérer la virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, améliorant ainsi les performances réseau. Quand vous activez la réplication pour une machine virtuelle Azure, Site Recovery détecte si l’accélération réseau est activée ou non. Si elle l’est, Site Recovery configure automatiquement l’accélération réseau sur la machine virtuelle Azure du réplica cible après le basculement, à la fois sur [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) et sur [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Plus d’informations](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculatrice de prix pour la reprise d’activité de machines virtuelles Azure

La reprise d’activité de machines virtuelles Azure occasionne des coûts liés aux licences des machines virtuelles ainsi que des frais de stockage et de réseau. Azure fournit une [calculatrice de prix](https://aka.ms/a2a-cost-estimator) pour vous aider à déterminer ces coûts. Site Recovery propose désormais un [exemple d’estimation du prix](https://aka.ms/a2a-cost-estimator) d’un déploiement basé sur une application à trois niveaux comprenant six machines virtuelles avec 12 disques HDD Standard et 6 disques SSD Premium. L’exemple suppose des changements de données totalisant 10 Go par jour pour le stockage Standard et 20 Go pour le stockage Premium. Pour estimer les coûts de votre propre déploiement, changez les variables. Vous pouvez spécifier le nombre de machines virtuelles, le nombre et le type de disques managés ainsi que le taux de changement de données total attendu sur les machines virtuelles. Vous pouvez également appliquer un facteur de compression pour estimer les coûts de bande passante. [Lisez](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l’annonce.



## <a name="q3-2018"></a>Troisième trimestre 2018 


### <a name="update-rollup-28-august-2018"></a>Mise à jour cumulative 28 (août 2018)

[Correctif cumulatif 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Récupération d’urgence pour Linux** | **Machines virtuelles Azure** : Ajouté la prise en charge pour Red Hat Enterprise Linux 6.10 ; CentOS 6.10.<br/><br/> **Les machines virtuelles VMware**: Red Hat Enterprise Linux 6.10 ; CentOS 6.10.<br/><br/> Machines virtuelles Linux utiliser le style de partition GUID partition GPT (table) en mode de compatibilité BIOS hérité sont désormais pris en charge. Consultez [Forum aux questions sur les disques de machine virtuelle Azure IaaS](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/faq-for-disks) pour plus d’informations. 
**Prise en charge du cloud** | Prise en charge la récupération d’urgence pour les machines virtuelles Azure dans le cloud allemand.
**Récupération d’urgence entre abonnements** | Prise en charge pour la réplication des machines virtuelles Azure dans une région vers une autre région dans un autre abonnement, au sein du même client Azure Active Directory. [Plus d’informations](https://aka.ms/cross-sub-blog)
**Windows Server 2008** | Prise en charge pour la migration des machines en cours d’exécution de Windows Server 2008 R2/2008 64 bits et 32 bits.<br/><br/> Migration uniquement (réplication et basculement). La restauration automatique n’est pas pris en charge.

### <a name="update-rollup-27-july-2018"></a>Correctif cumulatif 27 (juillet 2018)

[Correctif cumulatif 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Récupération d’urgence pour Linux** | **Machines virtuelles Azure** : Red Hat Enterprise Linux 7.5<br/><br/> **Serveurs de machines virtuelles VMware/physique**: Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>Étapes suivantes

Pour vous tenir informé de nos mises à jour, consultez la page [Mises à jour Azure](https://azure.microsoft.com/updates/?product=site-recovery).




 









