---
title: Matrice de prise en charge de la reprise d’activité des machines virtuelles VMware et serveurs physiques sur Azure avec Azure Site Recovery | Microsoft Docs
description: Récapitule les systèmes d’exploitation et composants pris en charge pour la reprise d’activité des machines virtuelles VMware et serveurs physiques sur Azure à l’aide d’Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 8be028d11d0778c2b67788029aa400ffd3b98cb4
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872918"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice de prise en charge de la reprise d’activité des machines virtuelles VMware et serveurs physiques sur Azure

Cet article répertorie les composants et les paramètres pris en charge pour la reprise après sinistre de machines virtuelles VMware vers Azure, avec [Azure Site Recovery](site-recovery-overview.md).

Pour commencer à utiliser Azure Site Recovery avec le scénario de déploiement la plus simple, consultez nos [didacticiels](tutorial-prepare-azure.md). Cliquez [ici](vmware-azure-architecture.md) pour en savoir plus sur l’architecture Azure Site Recovery.

## <a name="deployment-scenario"></a>Scénario de déploiement

**Scénario** | **Détails**
--- | ---
Récupération d’urgence de machines virtuelles VMware | Réplication de machines virtuelles VMware locales dans Azure. Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Récupération d’urgence de serveurs physiques | Réplication de serveurs physiques Windows/Linux locaux sur Azure. Vous pouvez déployer ce scénario dans le portail Azure.

## <a name="on-premises-virtualization-servers"></a>Serveurs de virtualisation locaux

**Serveur** | **Configuration requise** | **Détails**
--- | --- | ---
VMware | Serveur vCenter 6.7, 6.5, 6.0 ou 5.5, ou vSphere 6.7, 6.5, 6.0 ou 5.5 | Nous vous recommandons d’utiliser une instance de serveur vCenter.<br/><br/> Nous vous recommandons d’héberger les hôtes vSphere et les serveurs vCenter dans le même réseau que le serveur de traitement. Par défaut, les composants du serveur de traitement s’exécutent sur le serveur de configuration. Vous devrez donc opter pour le réseau dans lequel vous installez le serveur de configuration, à moins que vous n’installiez un serveur de traitement dédié.
Physique | N/A

## <a name="site-recovery-configuration-server"></a>Serveur de configuration Site Recovery

Le serveur de configuration est une machine locale qui exécute les composants de Site Recovery, comprenant le serveur de configuration, le serveur de traitement et le serveur cible maître. Pour la réplication de machines virtuelles VMware, vous installez le serveur de configuration avec tous les éléments requis, en utilisant un modèle OVF pour créer une machine virtuelle VMware. Dans le cas de la réplication de serveurs physiques, vous installez la machine du serveur de configuration manuellement.

**Composant** | **Configuration requise**
--- |---
Cœurs d’unité centrale | 8
RAM | 16 Go
Nombre de disques | 3 disques<br/><br/> Les disques comprennent le disque du système d’exploitation, le disque de cache du serveur de traitement et le lecteur de rétention pour la restauration automatique.
Espace disque libre | 600 Go d’espace requis pour le cache du serveur de traitement.
Espace disque libre | 600 Go d’espace requis pour le lecteur de rétention.
Système d’exploitation  | Windows Server 2012 R2 ou Windows Server 2016 |
Paramètres régionaux du système d’exploitation | Anglais (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") n’est pas obligatoire pour le serveur de configuration avec les versions de [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery).
Rôles Windows Server | N’activez pas les éléments suivants : <br/> - Active Directory Domain Services <br/>- Internet Information Services <br/> - Hyper-V |
Stratégies de groupe| N’activez pas les éléments suivants : <br/> - Empêcher l’accès à l’invite de commandes <br/> - Empêcher l’accès aux outils de modification du Registre <br/> - Logique de confiance pour les pièces jointes <br/> - Activer l’exécution des scripts <br/> [En savoir plus](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assurez-vous d’effectuer les tâches suivantes :<br/><br/> - Vérifier l’absence d’un site web par défaut préexistant <br/> - Activer [l’authentification anonyme](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Activer le paramètre [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Vérifier qu’aucune application/aucun site web préexistants n’écoutent le port 443<br/>
Type de carte réseau | VMXNET3 (en cas de déploiement comme machine virtuelle VMware)
Type d’adresse IP | statique
Ports | 443 utilisé pour l’orchestration du canal de contrôle<br/>9443 utilisé pour le transport de données

## <a name="replicated-machines"></a>Machines répliquées

Site Recovery assure la réplication de toutes les charges de travail exécutées sur une machine prise en charge.

**Composant** | **Détails**
--- | ---
Paramètres de la machine | Les ordinateurs qui répliquent vers Azure doivent répondre aux [conditions requises par Azure](#azure-vm-requirements).
Charge de travail de machine | Site Recovery assure la réplication de toutes les charges de travail exécutées (par exemple, Active Directory, SQL Server, etc.) sur une machine prise en charge. [Plus d’informations](https://aka.ms/asr_workload)
Système d’exploitation Windows | Windows Server 2016 64 bits (Server Core, Server avec Expérience utilisateur), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1. </br></br>  [Windows Server 2008 avec au moins SP2 - 32 bits et 64 bits](migrate-tutorial-windows-server-2008.md) (migration uniquement). </br></br> Windows 2016 Nano Server n’est pas pris en charge.
Architecture de système d’exploitation Linux | Seul système 64 bits est pris en charge. système 32 bits n’est pas pris en charge.
Système d’exploitation Linux | Red Hat Enterprise Linux : 5.2 à 5.11<b>\*\*</b>, 6.1 à 6.10<b>\*\*</b>, 7.0 à 7.6 <br/><br/>CentOS : 5.2 à 5.11<b>\*\*</b>, 6.1 à 6.10<b>\*\*</b>, 7.0 à 7.6 <br/><br/>Serveur Ubuntu 14.04 LTS [(prise en charge des versions du noyau)](#ubuntu-kernel-versions)<br/><br/>Serveur Ubuntu 16.04 LTS [(prise en charge des versions du noyau)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8 [(prise en charge des versions du noyau)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(prise en charge des versions du noyau)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3, 4 et 5 (UEK3, UEK4, UEK5) <br/><br/></br>-La mise à niveau de machines répliquées de SUSE Linux Enterprise Server 11 SP3 vers SP4 n’est pas pris en charge. Pour effectuer la mise à niveau, désactivez la réplication et réactiver-la après la mise à niveau.</br></br> - [En savoir plus](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sur la prise en charge de Linux et des technologies open source dans Azure. Site Recovery orchestre le basculement pour exécuter des serveurs Linux dans Azure. Toutefois, les fournisseurs Linux peuvent limiter la prise en charge aux versions de distribution qui n’ont pas atteint leur fin de vie.<br/><br/> -Sur les distributions Linux, seuls les noyaux de stockage qui font partie de la version/mise à jour mineure de distribution sont pris en charge.<br/><br/> -La mise à niveau des machines protégées sur des versions de distribution majeures Linux n’est pas prise en charge. Pour effectuer la mettre à niveau, désactivez la réplication, mettez à niveau le système d’exploitation, puis réactivez la réplication.<br/><br/> Les serveurs exécutant Red Hat Enterprise Linux 5.2 à 5.11 ou CentOS 5.2 à 5.11 doivent avoir les [composants Linux Integration Services(LIS)](https://www.microsoft.com/download/details.aspx?id=55106) installés pour que les machines démarrent dans Azure.


### <a name="ubuntu-kernel-versions"></a>Version du noyau Ubuntu


**Version prise en charge** | **Version du service Mobilité Azure Site Recovery** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | [9,24] [9,24 UR] | 3.13.0-24-Generic à 3.13.0-167-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-Generic à 4.4.0-143-generic,<br/>4.15.0-1023-Azure à 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic à 3.13.0-165-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-Generic à 4.4.0-142-generic,<br/>4.15.0-1023-Azure à 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-generic à 3.13.0-164-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-140-generic,<br/>4.15.0-1023-azure à 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-generic à 3.13.0-163-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-140-generic,<br/>4.15.0-1023-azure à 4.15.0-1035-azure |
|||
LTS 16.04 | [9.23] [9,24 UR] | 4.4.0-21-Generic à 4.4.0-143-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-Generic à 4.15.0-46-generic<br/>4.11.0-1009-Azure à 4.11.0-1018-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-Azure à 4.15.0-1040-azure|
LTS 16.04 | [9.23][9.23 UR] | 4.4.0-21-Generic à 4.4.0-142-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-Generic à 4.15.0-45-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-Azure à 4.15.0-1037-azure|
LTS 16.04 | [9.22][9.22 UR] | 4.4.0-21-generic à 4.4.0-140-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-43-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1036-azure|
LTS 16.04 | [9.21][9.21 UR] | 4.4.0-21-generic à 4.4.0-140-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-42-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1035-azure|
LTS 16.04 | [9.20][9.20 UR] | 4.4.0-21-generic à 4.4.0-138-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-38-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1025-azure|

### <a name="debian-kernel-versions"></a>Versions du noyau Debian


**Version prise en charge** | **Version du service Mobilité Azure Site Recovery** | **Version du noyau** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9,24] [UR 9.24]| 3.2.0-4-amd64 à 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.21][9.21 UR],[9.22][9.22 UR],[9.23][9.23 UR], [9,24] [UR 9.24] | 3.16.0-4-amd64 à 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Versions du noyau prises en charge de SUSE Linux Enterprise Server 12

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,24] [9,24 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) à 4.4.121-92.101-default</br></br>4.4.73-5-default SP3 pour 4.4.175-94.79-default</br></br>4.12.14-94.41-default SP4 à 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.23][9.23 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>4.4.121-92.73-default SP2(LTSS) à 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default à 4.4.162-94.69-default</br></br>4.12.14-94.41-default SP4 à 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default à 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.21][9.21 UR] | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default à 4.4.156-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Stockage invité/système de fichiers Linux

**Composant** | **Pris en charge**
--- | ---
Systèmes de fichiers | ext3, ext4, XFS
Gestionnaire de volume | Avant la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM est pris en charge. <br/> 2. /boot sur un volume LVM n’est pas pris en charge. <br/> 3. Plusieurs disques de système d’exploitation ne sont pas pris en charge.<br/><br/>À partir de [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) et versions ultérieures, /boot sur LVM est pris en charge. Plusieurs disques de système d’exploitation ne sont pas pris en charge.
Dispositif de stockage paravirtualisé | Les appareils exportés par les pilotes paravirtualisés ne sont pas pris en charge.
Unités de bloc d’entrée et de sortie en file d’attente | Non pris en charge.
Serveurs physiques avec le contrôleur de stockage HP CCISS | Non pris en charge.
Convention de nommage pour les appareils/points de montage | Le nom de l’appareil ou le nom du point de montage doit être unique. Vérifiez que deux appareils/points de montage n’ont pas des noms identiques avec une différence de casse. </br> Exemple : Nommer deux appareils de la même machine virtuelle *appareil1* et *Appareil1* n’est pas autorisé.
Répertoires | Avant la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. Les répertoires suivants (s’ils sont configurés en tant que partitions/systèmes de fichiers séparés) doivent tous se trouver sur le même disque du système d’exploitation, sur le serveur source : /(root), /boot, /usr, /usr/local, /var, /etc.</br>2. /boot doit se trouver sur une partition de disque et ne doit pas être un volume LVM.<br/><br/> Depuis la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), les restrictions ci-dessus ne sont pas applicables. /boot sur un volume LVM sur plusieurs disques n’est pas pris en charge.
Répertoire de démarrage | Les machines virtuelles comprenant plusieurs disques de démarrage ne sont pas prises en charge. <br/><br/> Une machine sans disque de démarrage ne peut pas être protégée.
Espace libre requis| 2 Go sur la partition/root <br/><br/> 250 Mo dans le dossier d’installation
XFSv5 | Les fonctionnalités XFSv5 sur des systèmes de fichiers XFS, tels que les sommes de contrôle de métadonnées, sont prises en charge à partir du service Mobilité version 9.10 et versions ultérieures. Utilisez l’utilitaire xfs_info pour vérifier le superbloc XFS pour la partition. Si `ftype` est défini sur 1, puis les fonctionnalités XFSv5 sont en cours d’utilisation.
BTRFS |À partir de la version 9.22, BTRFS est pris en charge, à l’exception des scénarios suivants</br>Si le sous-volume de système de fichiers BTRFS est modifié après l’activation de protection, BTRFS n’est pas pris en charge. </br>Si le système de fichiers BTRFS est réparti sur plusieurs disques, BTRFS n’est pas pris en charge.</br>Si le système de fichiers BTRFS prend en charge RAID, BTRFS n’est pas pris en charge.

## <a name="vmdisk-management"></a>Gestion des machines virtuelles/disques

**Action** | **Détails**
--- | ---
Redimensionner le disque sur la machine virtuelle répliquée |  Pris en charge.
Ajouter un disque à la machine virtuelle répliquée | Désactivez la réplication pour la machine virtuelle, ajoutez le disque, puis réactivez la réplication. L’ajout d’un disque sur une machine virtuelle de réplication n’est pas pris en charge pour l’instant.

## <a name="network"></a>Réseau

**Composant** | **Pris en charge**
--- | ---
Association de cartes réseau de réseau hôte | Pris en charge pour les machines virtuelles VMware <br/><br/>Non pris en charge pour la réplication des machines physiques
Réseau hôte VLAN | Oui.
Réseau hôte IPv4 | Oui.
Réseau hôte IPv6 |  Non.
Association de cartes de réseau invité/serveur |  Non.
Réseau invité/serveur IPv4 | Oui.
Réseau invité/serveur IPv6 |  Non.
Adresse IP statique du réseau invité/serveur (Windows) | Oui.
Adresse IP statique du réseau invité/serveur (Linux) | Oui. <br/><br/>Les machines virtuelles sont configurées pour utiliser le protocole DHCP lors de la restauration automatique.
Plusieurs cartes réseau invité/serveur | Oui.


## <a name="azure-vm-network-after-failover"></a>Réseau de machines virtuelles Azure (après le basculement)

**Composant** | **Pris en charge**
--- | ---
Azure ExpressRoute | OUI
ILB | OUI
ELB | OUI
Azure Traffic Manager | OUI
Plusieurs cartes réseau | OUI
Adresses IP réservées | OUI
IPv4 | OUI
Conserver l’adresse IP source | OUI
Points de terminaison du service Réseau virtuel Azure<br/> | Oui
Mise en réseau accélérée | Non 

## <a name="storage"></a>Stockage
**Composant** | **Pris en charge**
--- | ---
Disque dynamique | Le disque du système d’exploitation doit être un disque de base. <br/><br/>Les disques de données peuvent être des disques dynamiques
Configuration des disques Docker | Non 
Hôte NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques
Hôte SAN (iSCSI/FC) | OUI
vSAN hôte | Oui pour VMware<br/><br/> N/A pour les serveurs physiques
Multipath hôte (MPIO) | Oui, testé avec : Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pour CLARiiON
Volumes virtuels hôtes (VVols) | Oui pour VMware<br/><br/> N/A pour les serveurs physiques
VMDK invité/serveur | Oui
Disque de cluster partagé invité/serveur | Non 
Disque chiffré invité/serveur | Non 
NFS invité/serveur | Non 
SMB 3.0 invité/serveur | Non 
RDM invité/serveur | OUI<br/><br/> N/A pour les serveurs physiques
Disque invité/serveur > 1 To | OUI<br/><br/>Jusqu’à 4 095 Go<br/><br/> Le disque doit être d’une taille supérieure à 1 024 Mo.
Disque invité/serveur avec une taille de secteur logique de 4 Ko et une taille de secteur physique de 4 K | OUI
Disque invité/serveur avec une taille de secteur logique de 4 K et une taille de secteur physique de 512 octets | OUI
Volume invité/serveur avec disque à bandes > 4 To <br/><br/>Gestion des volumes logiques (LVM)| OUI
Invité/serveur - Espaces de stockage | Non 
Ajout/retrait à chaud de disque d’Invité/de serveur | Non 
Invité/serveur - Exclure le disque | OUI
Multipath invité/serveur (MPIO) | Non 
Démarrage EFI/UEFI invité/serveur | Prise en charge lors de la migration des machines virtuelles VMware ou serveurs physiques exécutant Windows Server 2012 ou version ultérieure vers Azure.<br/><br/> Vous pouvez uniquement répliquer des machines virtuelles pour la migration. La restauration automatique en local n’est pas pris en charge.<br/><br/> Le disque de système d’exploitation du serveur ne doit pas comprendre plus de 4 partitions.<br/><br/> Nécessite la version 9.13 du service Mobilité d’Azure Site Recovery, ou une version ultérieure.<br/><br/> Seul le système NTFS est pris en charge.

## <a name="replication-channels"></a>Canaux de réplication

|**Type de réplication**   |**Pris en charge**  |
|---------|---------|
|Transferts de données déchargées (ODX)    |       Non   |
|Amorçage hors connexion        |   Non       |
| Azure Data Box | Non 


## <a name="azure-storage"></a>Stockage Azure

**Composant** | **Pris en charge**
--- | ---
Stockage localement redondant | OUI
Stockage géo-redondant | OUI
Stockage géo-redondant avec accès en lecture | OUI
Stockage froid | Non 
Stockage chaud| Non 
Objets blob de blocs | Non 
Chiffrement au repos (Storage Service Encryption)| OUI
Stockage Premium | OUI
Service Import/Export | Non 
Pare-feu et réseaux virtuels de stockage Azure configurés dans le compte de stockage de cache/de stockage cible (utilisé pour stocker les données de réplication) | Oui
Comptes de stockage v2 à usage général (niveaux chaud et froid) | Non 

## <a name="azure-compute"></a>Calcul Azure

**Fonctionnalité** | **Pris en charge**
--- | ---
Groupes à haute disponibilité | Oui
Zones de disponibilité | Non 
CONCENTRATEUR | OUI
Disques managés | OUI

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les exigences des machines virtuelles Azure décrites dans ce tableau. Lorsque Site Recovery vérifie la configuration requise, il échoue si certaines conditions requises ne sont pas remplies.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
Système d’exploitation invité | Vérifiez les [systèmes d’exploitation pris en charge](#replicated-machines) pour les machines répliquées. | La vérification est mise en échec en cas de défaut de prise en charge.
Architecture du système d’exploitation invité | 64 bits. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go. | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques de données | 64 ou moins. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille de disque de données | Jusqu’à 4 095 Go | La vérification est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Disque dur virtuel partagé | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
BitLocker | Non pris en charge. | Vous devez désactiver BitLocker avant d’activer la réplication pour une machine. |
nom de la machine virtuelle | De 1 et 63 caractères.<br/><br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre. |  Mettez à jour la valeur dans les propriétés de machine de Site Recovery.

## <a name="azure-site-recovery-churn-limits"></a>Limites d’activité Azure Site Recovery

Le tableau suivant présente les limites d’Azure Site Recovery. Les limites sont basées sur nos tests, mais ne peuvent pas couvrir toutes les combinaisons d’E/S d’application possibles. Les résultats réels varient en fonction de la combinaison d’E/S de votre application. Pour de meilleurs résultats, nous recommandons fortement de [exécuter l’outil deployment planner](site-recovery-deployment-planner.md) et effectuer des tests en émettant un test de basculement d’application approfondis pour obtenir une image réelle des performances de l’application.

**Stockage de réplication cible** | **Taille d’E/S moyenne de disque source** |**Activité des données moyenne de disque source** | **Total de l’activité des données de disque source par jour**
---|---|---|---
Stockage Standard | 8 Ko | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 8 Ko  | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 16 Ko | 4 Mo/s |  336 Go par disque
Disque Premium P10 ou P15 | 32 Ko ou plus | 8 Mo/s | 672 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 8 Ko    | 5 Mo/s | 421 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 16 Ko ou plus |20 Mo/s | 1684 Go par disque

**Activité de données sources** | **Limite maximale**
---|---
Activité moyenne des données par machine virtuelle| 25 Mo/s
Activité des données de pointe sur tous les disques d’une machine virtuelle | 54 Mo/s
Activité des données maximale par jour prise en charge par un serveur de processus | 2 To

Il s’agit de moyennes en partant sur un chevauchement d’E/S de 30 pour cent. Site Recovery est capable de gérer un débit plus élevé en fonction du ratio de chevauchement, de tailles d’écriture plus grandes et du comportement d’E/S des charges de travail réelles. Les valeurs précédentes supposent un retard de traitement typique de cinq minutes. Autrement dit, une fois que les données sont chargées, elles sont traitées, et un point de récupération est créé dans un délai de cinq minutes.

## <a name="vault-tasks"></a>Tâches de coffre

**Action** | **Pris en charge**
--- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non 
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non 


## <a name="download-latest-azure-site-recovery-components"></a>Téléchargez les derniers composants Azure Site Recovery

**Name** | **Description** | **Instructions de téléchargement de la version la plus récente**
--- | --- | ---
Serveur de configuration | Coordonne les communications entre les serveurs VMware locaux et Azure  <br/><br/>  Installé sur des serveurs VMware locaux | Pour plus d’informations, consultez nos conseils sur [nouvelle installation](vmware-azure-deploy-configuration-server.md) et [mise à niveau d’un composant existant à la version la plus récente](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Serveur de traitement|Installé par défaut sur le serveur de configuration. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement, et les envoie vers le stockage Azure. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement distincts afin de gérer de plus grands volumes de trafic de réplication.| Pour plus d’informations, consultez nos conseils sur [nouvelle installation](vmware-azure-set-up-process-server-scale.md) et [mise à niveau d’un composant existant à la version la plus récente](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Service Mobilité | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer | Pour plus d’informations, consultez nos conseils sur [nouvelle installation](vmware-azure-install-mobility-service.md) et [mise à niveau d’un composant existant à la version la plus récente](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal).

Pour en savoir plus sur les fonctionnalités les plus récentes, visitez [dernières notes de publication](https://aka.ms/ASR_latest_release_notes).


## <a name="next-steps"></a>Étapes suivantes
[Découvrez comment](tutorial-prepare-azure.md) préparer Azure à la récupération d’urgence de machines virtuelles VMware.

[9.23 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
