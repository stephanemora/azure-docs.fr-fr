---
title: Prise en charge de la migration VMware dans Azure Migrate
description: Découvrez la prise en charge de la migration VMware dans Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: eea2ef1f84e5c31dd18ea4ef65ccf2796231352b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597981"
---
# <a name="support-matrix-for-vmware-migration"></a>Tableau de prise en charge pour la migration VMware

Cet article résume les paramètres et les limites de la migration de machines virtuelles VMware avec [Azure Migrate : Migration de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool). Si vous recherchez des informations sur l’évaluation de machines virtuelles VMware pour la migration vers Azure, consultez le [tableau de prise en charge de l’évaluation](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Options de migration

Vous pouvez migrer des machines virtuelles VMware de deux manières différentes :

- Avec une migration sans agent : Migrez des machines virtuelles sans avoir à installer quoi que ce soit. Vous déployez l’[appliance Azure Migrate](migrate-appliance.md) pour la migration sans agent.
- Avec une migration basée sur un agent : Installez un agent sur la machine virtuelle pour la réplication. Pour la migration basée sur un agent, vous devez déployer une [appliance de réplication](migrate-replication-appliance.md).

Consultez [cet article](server-migrate-overview.md) pour déterminer la méthode que vous souhaitez utiliser.

## <a name="migration-limitations"></a>Limitations de la migration

- Vous pouvez sélectionner jusqu’à 10 machines virtuelles à la fois pour la réplication. Si vous souhaitez migrer davantage de machines, répliquez-les dans des groupes de 10.
- Pour la migration sans agent VMware, vous pouvez exécuter jusqu’à 100 réplications simultanément.

## <a name="agentless-vmware-servers"></a>Serveurs VMware sans agent

**VMware** | **Détails**
--- | ---
**VMware vCenter Server** | Version 5.5, 6.0, 6.5 ou 6.7.
**Hôte ESXI VMware vSphere** | Version 5.5, 6.0, 6.5 ou 6.7.
**Autorisations vCenter Server** | La migration sans agent utilise l’[appliance Migrate](migrate-appliance.md). L’appliance a besoin des autorisations suivantes :<br/><br/> - **Datastore.Browse** : Autoriser l’exploration des fichiers journaux des machines virtuelles pour résoudre les problèmes de création et de suppression des instantanés.<br/><br/> - **Datastore.LowLevelFileOperations** : Autoriser les opérations de lecture/écriture/suppression/renommage dans l’explorateur de magasin de données pour résoudre les problèmes de création et de suppression des instantanés.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking** : Autoriser l’activation ou la désactivation du suivi des modifications des disques de machine virtuelle pour extraire les blocs de données modifiés entre les instantanés.<br/><br/> - **VirtualMachine.Configuration.DiskLease** : Autoriser les opérations de bail de disque pour une machine virtuelle, à lire le disque en utilisant VDDK (vSphere Virtual Disk Development Kit) VMware vSphere.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess** : (plus spécifiquement pour vSphere 6.0 et versions ultérieures) Autoriser l’ouverture d’un disque sur une machine virtuelle pour un accès en lecture aléatoire sur le disque à l’aide du VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess** : Autoriser l’ouverture d’un disque sur une machine virtuelle pour lire le disque avec VDDK.<br/><br/>- **VirtualMachine.Provisioning.AllowVirtualMachineDownload** : Autorise les opérations de lecture sur les fichiers associés à une machine virtuelle, à télécharger les journaux et à résoudre les problèmes en cas d’échec.<br/><br/> - **VirtualMachine.SnapshotManagement.*** : Autoriser la création et la gestion des instantanés de machines virtuelles pour la réplication.<br/><br/> - **Virtual Machine.Interaction.Power Off** : Autoriser la mise hors tension de la machine virtuelle pendant la migration vers Azure.



## <a name="agentless-vmware-vms"></a>Machines virtuelles VMware sans agent

**Support** | **Détails**
--- | ---
**Systèmes d’exploitation pris en charge** | Les systèmes d’ exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) pris en charge par Azure peuvent être migrés avec une migration sans agent.
**Modifications nécessaires pour Azure** | Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure. Azure Migrate effectue automatiquement ces modifications pour les systèmes d’exploitation suivants :<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> Pour les autres systèmes d’exploitation, vous devez effectuer les ajustements manuellement avant la migration. Les articles pertinents contiennent des instructions sur la façon de procéder.
**Démarrage Linux** | Si /boot se trouve sur une partition dédiée, il doit être le disque du système d’exploitation et ne pas être réparti sur plusieurs disques.<br/> Si /boot fait partie de la partition racine (/), la partition « / » doit se trouver sur le disque du système d’exploitation et ne pas s’étendre sur d’autres disques.
**Démarrage UEFI** | Les machines virtuelles avec démarrage UEFI ne sont pas prises en charge pour la migration.
**Taille du disque** | Disque de système d’exploitation de 2 To ; 4 To pour des disques de données.
**Limites du disque** |  Jusqu'à 60 disques par machine virtuelle.
**Disques/volumes chiffrés** | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration.
**Cluster de disque partagé** | Non pris en charge.
**Disques indépendants** | Non pris en charge.
**RDM/disques directs** | Si les machines virtuelles ont des disques RDM ou directs, ces disques ne seront pas répliqués dans Azure.
**NFS** | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués.
**Cibles iSCSI** | Les machines virtuelles avec des cibles iSCSI ne sont pas prises en charge pour la migration sans agent.
**Multipath I/O** | Non pris en charge.
**Storage vMotion** | Non pris en charge. La réplication ne fonctionne pas si une machine virtuelle utilise Storage vMotion.
**Cartes réseau associées** | Non pris en charge.
**IPv6** | Non pris en charge.
**Disque cible** | Les machines virtuelles peuvent être migrées seulement vers des disques managés (disques durs standard, disques SSD Premium) dans Azure.
**Réplication simultanée** | 100 machines virtuelles par serveur VCenter Server. Si vous en avez plus, migrez-les par lots de 100.


## <a name="agentless-azure-migrate-appliance"></a>Appliance Azure Migrate sans agent 
La migration sans agent utilise l’appliance Azure Migrate, déployée sur une machine virtuelle VMware.

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---vmware) pour VMware.
- En savoir plus sur les [URL](migrate-appliance.md#url-access) auxquelles l’appliance doit accéder.

## <a name="agentless-ports"></a>Ports sans agent

**Appareil** | **Connection**
--- | ---
Appliance | Connexions sortantes sur le port 443 pour charger les données répliquées sur Azure et pour communiquer avec les services Azure Migrate effectuant la réplication et la migration.
Serveur vCenter | Connexions entrantes sur le port 443 pour permettre à l’appliance d’orchestrer la réplication : créer des instantanés, copier des données, publier des instantanés
Hôte vSphere/EXSI | Trafic entrant sur le port TCP 902 pour que l’appliance réplique des données à partir d’instantanés.


## <a name="agent-based-vmware-servers"></a>Serveurs VMware basés sur un agent
Ce tableau récapitule la prise en charge et les limitations de l’évaluation pour les serveurs de virtualisation VMware.

**Conditions requises VMware** | **Détails**
--- | ---
**VMware vCenter Server** | Version 5.5, 6.0, 6.5 ou 6.7.
**Hôte ESXI VMware vSphere** | Version 5.5, 6.0, 6.5 ou 6.7.
**Autorisations vCenter Server** | Un compte en lecture seule pour vCenter Server.

## <a name="agent-based-vmware-vms"></a>Machines virtuelles VMware basées sur un agent

Le tableau résume la prise en charge de machines virtuelles VMware pour les machines virtuelles VMware que vous souhaitez migrer à l’aide de la migration basée sur un agent.

**Support** | **Détails**
--- | ---
**Charge de travail des machines** | Azure Migrate prend en charge la migration de toutes les charges de travail (par exemple Active Directory, SQL Server, etc.) exécutées sur une machine prise en charge.
**Systèmes d’exploitation** | Pour obtenir les informations les plus récentes, consultez [Prise en charge des systèmes d’exploitation](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pour Site Recovery. Azure Migrate fournit une prise en charge identique des systèmes d’exploitation des machines virtuelles.
**Stockage invité/système de fichiers Linux** | Pour obtenir les informations les plus récentes, consultez [Prise en charge des systèmes de fichiers Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pour Site Recovery. Azure Migrate a une prise en charge identique des systèmes de fichiers Linux.
**Réseau/Stockage** | Pour obtenir les informations les plus récentes, consultez les prérequis en matière de [réseau](../site-recovery/vmware-physical-azure-support-matrix.md#network) et de [stockage](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pour Site Recovery. Azure Migrate fournit des exigences identiques pour le réseau/stockage.
**Conditions requises pour Azure** | Pour obtenir les informations les plus récentes, consultez les exigences d’Azure en matière de [réseau](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), de [stockage](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage) et de [calcul](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) pour Site Recovery. Azure Migrate a des exigences identiques pour la migration de VMware.
**Service de mobilité** | L’agent du service Mobilité doit être installé sur chaque machine virtuelle que vous voulez migrer.
**Démarrage UEFI** | La machine virtuelle migrée dans Azure est automatiquement convertie en machine virtuelle de démarrage du BIOS.<br/><br/> Le disque du système d’exploitation doit avoir jusqu’à quatre partitions et les volumes doivent être formatés avec NTFS.
**Disque cible** | Les machines virtuelles peuvent être migrées seulement vers des disques managés (disques durs standard, disques SSD Premium) dans Azure.
**Taille du disque** | Disque de système d’exploitation de 2 To ; 8 To pour des disques de données.
**Limites du disque** |  Jusqu'à 63 disques par machine virtuelle.
**Disques/volumes chiffrés** | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration.
**Cluster de disque partagé** | Non pris en charge.
**Disques indépendants** | Pris en charge.
**Disques directs** | Pris en charge.
**NFS** | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués.
**Cibles iSCSI** | Les machines virtuelles avec des cibles iSCSI ne sont pas prises en charge pour la migration sans agent.
**Multipath I/O** | Non pris en charge.
**Storage vMotion** | Prise en charge
**Cartes réseau associées** | Non pris en charge.
**IPv6** | Non pris en charge.




## <a name="agent-based-replication-appliance"></a>Appliance de réplication basée sur un agent 

Quand vous configurez l’appliance de réplication en utilisant le modèle OVA fourni dans le hub Azure Migrate, l’appliance exécute Windows Server 2016 et est conforme aux exigences de la prise en charge. Si vous configurez manuellement l’appliance de réplication sur un serveur physique, vérifiez qu’elle est conforme aux exigences.

- En savoir plus sur les [conditions requises de l’appliance de réplication](migrate-replication-appliance.md#appliance-requirements) pour VMware.
- MySQL doit être installé sur l’appliance. En savoir plus sur les [options d’installation](migrate-replication-appliance.md#mysql-installation).
- Découvrez les [URL](migrate-replication-appliance.md#url-access) et les [ports](migrate-replication-appliance.md#port-access) auxquels l’appliance de réplication doit accéder.

## <a name="agent-based-ports"></a>Ports basés sur un agent

**Appareil** | **Connection**
--- | ---
Machines virtuelles | Le service Mobility en cours d’exécution sur des machines virtuelles communique avec l'appliance de réplication locale (serveur de configuration) via le port HTTPS 443 entrant, pour la gestion de la réplication.<br/><br/> Les machines virtuelles envoient des données de réplication au serveur de traitement (s’exécutant sur l’ordinateur du serveur de configuration) sur le port HTTPS 9443 entrant. Ce port peut être modifié.
Appliance de réplication | L’appliance de réplication orchestre la réplication avec Azure sur le port HTTPS 443 sortant.
Serveur de traitement | Le serveur de traitement reçoit les données de réplication, les optimise et les chiffre, puis les envoie au stockage Azure via le port 443 sortant.<br/> Le serveur de processus s’exécute par défaut sur l’appliance de réplication.

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Toutes les machines virtuelles locales répliquées sur Azure doivent respecter les exigences des machines virtuelles Azure récapitulées dans ce tableau. Quand Site Recovery vérifie les prérequis pour la réplication, la vérification échoue si certaines exigences ne sont pas satisfaites.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
Système d’exploitation invité | Vérifie les systèmes d’exploitation de machines virtuelles VMware pris en charge pour la migration.<br/> Vous pouvez migrer les charges de travail s’exécutant sur un système d’exploitation pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Architecture du système d’exploitation invité | 64 bits. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go. | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques de données | 64 ou moins. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille de disque de données | Jusqu’à 4 095 Go | La vérification est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Disque dur virtuel partagé | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
BitLocker | Non pris en charge. | Vous devez désactiver BitLocker avant d’activer la réplication pour une machine.
nom de la machine virtuelle | De 1 et 63 caractères.<br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre. |  Mettez à jour la valeur dans les propriétés de machine de Site Recovery.
Se connecter après la migration - Windows | Pour vous connecter à des machines virtuelles Azure exécutant Windows après la migration :<br/> - Avant la migration, active le protocole RDP sur la machine virtuelle locale. Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public** et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.<br/> Pour l’accès VPN site à site, activez RDP et autorisez RDP dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**. Vérifiez aussi que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [Plus d’informations](prepare-for-migration.md) |
Se connecter après la migration - Linux | Pour vous connecter à des machines virtuelles Azure après la migration avec SSH :<br/> Avant la migration, sur la machine locale, vérifiez que le service Secure Shell est défini sur Démarrer et que les règles de pare-feu autorisent une connexion SSH.<br/> Après le basculement, sur la machine virtuelle Azure, autorisez les connexions entrantes au port SSH pour les règles du groupe de sécurité réseau sur la machine virtuelle basculée, et pour le sous-réseau Azure auquel elle est connectée. Ajoutez aussi une adresse IP publique pour la machine virtuelle. |  


## <a name="next-steps"></a>Étapes suivantes

[Sélectionnez](server-migrate-overview.md) une option de migration VMware.
