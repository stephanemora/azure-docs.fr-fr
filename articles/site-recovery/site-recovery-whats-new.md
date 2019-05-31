---
title: Nouveautés d’Azure Site Recovery | Microsoft Docs
description: Fournit un récapitulatif des nouvelles fonctionnalités introduites dans Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 50e1cb95249f0108430e978ae3ffe23b6edc778d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418389"
---
# <a name="whats-new-in-site-recovery"></a>Nouveautés de Site Recovery

Le service [Azure Site Recovery](site-recovery-overview.md) fait l’objet de mises à jour et d’améliorations continues. Pour vous aider à rester à jour, cet article vous donne des informations sur les versions les plus récentes, les nouvelles fonctionnalités et le nouveau contenu. Cette page est régulièrement mise à jour.

Si vous avez des suggestions concernant les fonctionnalités de Site Recovery, [faites-nous part de vos commentaires](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-march-2019"></a>Mises à jour (2019 mars)

### <a name="update-rollup-35"></a>Mise à jour cumulative 35

[Correctif cumulatif 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif)
**Problème correctifs/améliorations** | Un nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif)

#### <a name="vmwarephysical-server-disaster-recovery"></a>Récupération d’urgence de VMware/serveur physique
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Disques gérés** | Réplication de machines virtuelles VMware locales sur des serveurs physiques est désormais directement vers des disques gérés dans Azure. En local données sont envoyées à un compte de stockage de cache dans Azure et les points de récupération sont créés dans des disques gérés dans l’emplacement cible. Cela garantit que vous n’avez pas besoin de gérer plusieurs comptes de stockage cible.
**Serveur de configuration** | Site Recovery prend désormais en charge les serveurs d’une configuration avec plusieurs cartes réseau. Vous devez ajouter des cartes supplémentaires à la machine virtuelle du serveur de configuration avant d’inscrire le serveur de configuration dans le coffre. Si vous ajoutez par la suite, vous devez réinscrire le serveur dans le coffre.


## <a name="updates-february-2019"></a>Mises à jour (février 2019)

### <a name="update-rollup-34"></a>Mise à jour cumulative 34 

[Correctif cumulatif 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).


### <a name="update-rollup-33"></a>Mise à jour cumulative 33 

[Correctif cumulatif 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).


#### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machines virtuelles Azure 
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Mappage réseau** | Pour la récupération d’urgence de machines virtuelles Azure, vous pouvez maintenant utiliser n’importe quel réseau cible disponible lorsque vous activez la réplication. 
**Disque SSD standard** | Vous pouvez maintenant définir la récupération d’urgence pour les machines virtuelles Azure à l’aide [disques SSD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaces de stockage Direct** | Vous pouvez configurer la récupération d’urgence pour les applications qui s’exécutent sur les applications de machine virtuelle Azure à l’aide de [espaces de stockage Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pour la haute disponibilité.  À l’aide d’espaces de stockage Direct (S2D) ainsi que Site Recovery fournit une protection complète des charges de travail de machine virtuelle Azure. S2D vous permet d’héberger un cluster invité dans Azure. Cela est particulièrement utile lorsqu’une machine virtuelle héberge une application critique, telles que SAP ASCS couche, SQL Server ou serveur de fichiers avec montée en puissance.


#### <a name="vmwarephysical-server-disaster-recovery"></a>Récupération d’urgence de VMware/serveur physique
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Système de fichiers Linux BRTFS** | Site Recovery prend désormais en charge la réplication des machines virtuelles VMware avec le système de fichiers BRTFS. La réplication n’est pas pris en charge si :<br/><br/>-Le sous-volume de système de fichiers BTRFS est modifié après l’activation de la réplication.<br/><br/>-Le système de fichiers est réparti sur plusieurs disques.<br/><br/>-Le système de fichiers BTRFS prend en charge RAID.
**Windows Server 2019** | Prise en charge ajoutée pour les ordinateurs exécutant Windows Server 2019.


## <a name="updates-january-2019"></a>Mises à jour (janvier 2019)

### <a name="accelerated-networking-azure-vms"></a>Mise en réseau accélérée (machines virtuelles Azure)

L’accélération réseau permet d’opérer la virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, améliorant ainsi les performances réseau. Quand vous activez la réplication pour une machine virtuelle Azure, Site Recovery détecte si l’accélération réseau est activée ou non. Si elle l’est, Site Recovery configure automatiquement l’accélération réseau sur la machine virtuelle Azure du réplica cible après le basculement, à la fois sur [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) et sur [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Plus d’informations](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="update-rollup-32"></a>Mise à jour cumulative 32 

[Correctif cumulatif 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).

#### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machines virtuelles Azure

Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Prise en charge a été ajoutée pour la station de travail Red Hat 6/7 et les nouvelles versions de noyau pour Ubuntu, Debian et SUSE.
**Espaces de stockage Direct** | Site Recovery prend en charge les machines virtuelles Azure à l’aide d’espaces de stockage Direct (S2D).

#### <a name="vmware-vmsphysical-servers-replication"></a>Réplication de serveurs de machines virtuelles VMware/physique 
**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Prise en charge a été ajoutée pour Red Hat Enterprise Linux 7.6, station de travail Red Hat 6/7, Oracle Linux 6.10/7.6 et nouvelles versions de noyau pour Ubuntu, Debian et SUSE.


### <a name="update-rollup-31"></a>Mise à jour cumulative 31 

[Correctif cumulatif 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).

#### <a name="vmware-vmsphysical-servers-replication"></a>Réplication de serveurs de machines virtuelles VMware/physique 
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Prise en charge a été ajoutée pour Oracle Linux 6.8 et 6.9/7.0 et le noyau UEK5.
**LVM** | Prend désormais en charge des volumes LVM et LVM2.<br/><br/> Le répertoire /boot sur une partition de disque et sur des volumes LVM est maintenant pris en charge.
**Directories** | Prise en charge a été ajoutée pour ces répertoires configurés en tant que partitions distinctes ou des systèmes de fichiers qui ne sont pas sur le même disque de système :<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Prend désormais en charge les disques dynamiques.
**Type de basculement** | Amélioration des temps de basculement où storvsc et vsbus ne sont pas des pilotes de démarrage des machines virtuelles VMware.
**Prise en charge UEFI** | Les machines virtuelles Azure ne prennent pas en charge le type de démarrage UEFI. Vous pouvez désormais migrer des serveurs physiques locaux avec UEFI vers Azure avec Site Recovery. Site Recovery migre le serveur en convertissant le type de démarrage en BIOS avant la migration. Site Recovery précédemment pris en charge cette conversion pour les machines virtuelles uniquement. Prise en charge est disponible pour les serveurs physiques exécutant Windows Server 2012 ou version ultérieure.

#### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machines virtuelles Azure
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Prise en charge a été ajoutée pour Oracle Linux 6.8 et 6.9/7.0 ; et pour le noyau UEK5.
**Système de fichiers Linux BRTFS** | Prise en charge pour les machines virtuelles Azure.
**Machines virtuelles Azure dans les zones de disponibilité** | Vous pouvez activer la réplication vers une autre région pour machines virtuelles Azure déployées dans les zones de disponibilité. Vous pouvez désormais activer la réplication pour une machine virtuelle Azure et définir comme cible du basculement une instance de machine virtuelle unique, une machine virtuelle dans un groupe à haute disponibilité ou une machine virtuelle dans une zone de disponibilité. Le paramètre n’impacte pas la réplication. [Lisez](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l’annonce.
**Pare-feu activé le stockage (portal/PowerShell)** | Prise en charge ajoutée pour [comptes de stockage pare-feu activé](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Vous pouvez répliquer des machines virtuelles Azure avec disques non gérés sur les comptes de stockage pare-feu activé sur une autre région Azure de récupération d’urgence.<br/><br/> Vous pouvez utiliser des comptes de stockage pare-feu activé en tant que comptes de stockage cible pour les disques non gérés.<br/><br/> Prise en charge dans portail et à l’aide de PowerShell.

## <a name="updates-december-2018"></a>Mises à jour (décembre 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Mises à jour automatiques pour le service mobilité (machines virtuelles Azure)

Site Recovery propose désormais une option pour mettre à jour automatiquement l’extension du service Mobilité. L’extension du service Mobilité est installée sur chaque machine virtuelle Azure répliquée par Site Recovery. Quand vous activez la réplication, vous pouvez autoriser Site Recovery à gérer les mises à jour de l’extension.

Les mises à jour ne nécessitent pas le redémarrage de la machine virtuelle et n’affectent pas la réplication. [Plus d’informations](azure-to-azure-autoupdate.md)

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculatrice de prix pour la reprise d’activité de machines virtuelles Azure

Récupération d’urgence de machines Azure virtuelles entraîne des coûts de licence de machine virtuelle et les coûts de stockage et de réseau. Azure fournit une [calculatrice de prix](https://aka.ms/a2a-cost-estimator) pour vous aider à déterminer ces coûts. Site Recovery propose désormais un [exemple d’estimation du prix](https://aka.ms/a2a-cost-estimator) d’un déploiement basé sur une application à trois niveaux comprenant six machines virtuelles avec 12 disques HDD Standard et 6 disques SSD Premium.

- L’exemple suppose une modification de données de 10 Go un jour pour l’édition standard et 20 Go pour l’édition premium.
- Pour estimer les coûts de votre propre déploiement, changez les variables.
- Vous pouvez spécifier le nombre de machines virtuelles, le nombre et le type de disques managés ainsi que le taux de changement de données total attendu sur les machines virtuelles.
- Vous pouvez également appliquer un facteur de compression pour estimer les coûts de bande passante.

[Lisez](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l’annonce.


## <a name="updates-october-2018"></a>Mises à jour (octobre 2018)

### <a name="update-rollup-30"></a>Mise à jour cumulative 30 

[Correctif cumulatif 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).

#### <a name="azure-vm-disaster-recovery"></a>Récupération d’urgence de machines virtuelles Azure
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de la région** | Site Recovery prise en charge de 1 Central de l’Australie et en Australie centrale 2.
**Prise en charge pour le chiffrement de disque** | Prise en charge ajoutée pour la récupération d’urgence de machines virtuelles Azure chiffrées avec Azure Disk Encryption (ADE) avec l’application Azure AD. [Plus d’informations](azure-to-azure-how-to-enable-replication-ade-vms.md)
**Exclusion de disque** | Disques non initialisées sont maintenant automatiquement exclus lors de la réplication de machine virtuelle Azure.
**Pare-feu activé le stockage (PowerShell)** | Prise en charge ajoutée pour [comptes de stockage pare-feu activé](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Vous pouvez répliquer des machines virtuelles Azure avec disques non gérés sur les comptes de stockage pare-feu activé sur une autre région Azure de récupération d’urgence.<br/><br/> Vous pouvez utiliser des comptes de stockage pare-feu activé en tant que comptes de stockage cible pour les disques non gérés.<br/><br/> Prise en charge à l’aide de PowerShell uniquement.


### <a name="update-rollup-29"></a>Mise à jour cumulative 29 

[Correctif cumulatif 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).


## <a name="updates-august-2018"></a>Mises à jour (août 2018)

### <a name="update-rollup-28"></a>Correctif cumulatif 28 

[Correctif cumulatif 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).

#### <a name="azure-vms-disaster-recovery"></a>Récupération d’urgence de machines virtuelles Azure 
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Ajouté la prise en charge pour Red Hat Enterprise Linux 6.10 ; CentOS 6.10.<br/><br/>
**Prise en charge du cloud** | Prise en charge la récupération d’urgence pour les machines virtuelles Azure dans le cloud allemand.
**Récupération d’urgence entre abonnements** | Prise en charge pour la réplication des machines virtuelles Azure dans une région vers une autre région dans un autre abonnement, au sein du même client Azure Active Directory. [Plus d’informations](https://aka.ms/cross-sub-blog)

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Récupération d’urgence de machines virtuelles VMware/serveur physique 
Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Prise en charge de Red Hat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Machines virtuelles Linux utiliser le style de partition GUID partition GPT (table) en mode de compatibilité BIOS hérité sont désormais pris en charge. Examinez le [Forum aux questions de la machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) pour plus d’informations. 
**Récupération d’urgence pour les machines virtuelles après la migration** | Prise en charge pour l’activation de la récupération d’urgence vers une région secondaire pour une machine virtuelle de VMware sur site migrées vers Azure, sans avoir à désinstaller la mobilité de service sur la machine virtuelle avant d’activer la réplication.
**Windows Server 2008** | Prise en charge pour la migration des machines en cours d’exécution de Windows Server 2008 R2/2008 64 bits et 32 bits.<br/><br/> Migration uniquement (réplication et basculement). La restauration automatique n’est pas pris en charge.

## <a name="updates-july-2018"></a>Mises à jour (juillet 2018)

### <a name="update-rollup-27-july-2018"></a>Correctif cumulatif 27 (juillet 2018)

[Correctif cumulatif 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fournit les mises à jour suivants.

**Mettre à jour** | **Détails**
--- | ---
**Fournisseurs et agents** | Une mise à jour des agents de récupération de Site et aux fournisseurs (comme décrit dans le correctif cumulatif).
**Problème correctifs/améliorations** | Nombre de correctifs et améliorations (comme décrit dans le correctif cumulatif).

#### <a name="azure-vms-disaster-recovery"></a>Récupération d’urgence de machines virtuelles Azure 

Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Prise en charge de Red Hat Enterprise Linux 7.5.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Récupération d’urgence de machines virtuelles VMware/serveur physique 

Nouvelles fonctionnalités ajoutées dans la mise à jour.

**Fonctionnalité** | **Détails**
--- | ---
**Prise en charge de Linux** | Prise en charge de Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Étapes suivantes

Pour vous tenir informé de nos mises à jour, consultez la page [Mises à jour Azure](https://azure.microsoft.com/updates/?product=site-recovery).
