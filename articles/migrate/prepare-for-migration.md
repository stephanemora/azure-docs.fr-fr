---
title: Préparer des machines pour la migration avec Azure Migrate
description: Découvrez comment préparer des machines locales pour la migration avec Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2c13bc79fccc74da431ba1a6399870b261b24c57
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196437"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Préparer des ordinateurs locaux à une migration vers Azure

Cet article explique comment préparer des ordinateurs locaux avant de lancer leur migration vers Azure à l’aide de la fonctionnalité [Migration de serveur Azure Migrate](migrate-services-overview.md#azure-migrate-server-migration-tool).


Dans cet article, vous découvrirez comment :
> [!div class="checklist"]
> * Vérifier les limitations de la migration.
> * Vérifier la configuration requise pour le système d’exploitation et les limites de prise en charge.
> * Examiner l’accès aux URL/ports pour les ordinateurs que vous voulez migrer.
> * Passer en revue les modifications que vous devrez peut-être apporter avant de commencer la migration.
> * Configurer les paramètres de telle sorte que les lettres de lecteurs soient conservées après la migration.
> * Préparer les ordinateurs de telle sorte que vous puissiez vous connecter aux machines virtuelles Azure après la migration.


## <a name="verify-migration-limitations"></a>Vérifier les limitations de la migration

- Vous pouvez évaluer jusqu’à 35 000 machines virtuelles VMware/Hyper-V dans un même projet Azure Migrate à l’aide de la fonctionnalité Migration de serveur Azure Migrate. Un projet peut inclure à la fois des machines virtuelles de type VMware et Hyper-V, dans les limites de chacun d’eux.
- Vous pouvez sélectionner jusqu’à 10 machines virtuelles à la fois pour la migration. Si vous devez en répliquer davantage, répliquez-les dans des groupes de 10.
- Pour la migration sans agent VMware, vous pouvez exécuter jusqu’à 100 réplications simultanément.

## <a name="verify-operating-system-requirements"></a>Vérifier la configuration requise pour le système d’exploitation

- Vérifiez que vos [systèmes d’exploitation Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) sont pris en charge dans Azure.
- Vérifiez que vos [distributions Linux](../virtual-machines/linux/endorsed-distros.md) sont prises en charge dans Azure.


## <a name="check-whats-supported"></a>Vérifier ce qui est pris en charge

- Pour les machines virtuelles VMware, la migration de serveur Azure Migrate prend en charge la [migration avec ou sans agent](server-migrate-overview.md). Vérifiez la configuration requise/la prise en charge des machines virtuelles VMware pour les migrations [sans agent](migrate-support-matrix-vmware.md#migration---limitations) et [avec agent](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements).
- Vérifiez la [configuration requise et la prise en charge pour la migration](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) des machines virtuelles Hyper-V.
- Vérifiez la [configuration requise et la prise en charge pour la migration](migrate-support-matrix-physical.md) des machines physiques locales ou d’autres serveurs virtualisés. 




## <a name="review-urlport-access"></a>Vérifier l’accès aux URL/ports

Les ordinateurs peuvent avoir besoin d’un accès Internet pendant la migration.

- Examinez les URL auxquelles les machines virtuelles VMware doivent accéder pendant les migrations [sans agent](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) ou [avec agent](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
- Passez en revue les URL auxquelles les hôtes Hyper-V doivent accéder pendant la migration. Les machines virtuelles Hyper-V n’ont pas besoin d’accès Internet.
- [Examinez les URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) que les machines physiques ou autres serveurs virtualisés doivent accéder pendant la migration.
- Pendant la migration basée sur un agent de machines virtuelles/serveurs physiques VMware, le service Mobility s’exécutant sur les machines a besoin d’accéder à des composants Azure Migrate. Pour la gestion de la réplication, le service s’exécutant sur la machine communique avec l’appliance de réplication locale Azure Migrate sur le port HTTPS 443 entrant. Les machines envoient des données de réplication au serveur de processus Azure Migrate sur le port HTTPS 9443 entrant. Ce port peut être modifié.


## <a name="verify-required-changes-before-migration"></a>Vérifier les modifications nécessaires avant la migration

Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure. Azure Migrate effectue automatiquement ces modifications pour les machines virtuelles exécutant ces systèmes exploitation :
- Red Hat Enterprise Linux 6.5+, 7.0+
- CentOS 6.5+, 7.0+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
- Debian 7, 8

Pour les autres systèmes d’exploitation, vous devez préparer les machines manuellement avant la migration. 

### <a name="prepare-windows-machines"></a>Préparer les machines Windows

Si vous migrez une machine Windows, apportez ces modifications avant la migration. Si vous migrez la machine virtuelle avant d’effectuer les modifications, elle risque de ne pas démarrer dans Azure.

1. [Activez la console d’accès série Azure](../virtual-machines/troubleshooting/serial-console-windows.md) pour la machine virtuelle Azure. Cela facilite la résolution des problèmes. Vous n’avez pas besoin de redémarrer la machine virtuelle. La machine virtuelle Azure démarre en utilisant l’image de disque. Cela équivaut à un redémarrage de la nouvelle machine virtuelle. 
2. Si vous migrez des ordinateurs Windows Server 2003, installez les services d’intégration d’invité Hyper-V sur le système d’exploitation de la machine virtuelle. [Plus d’informations](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)

### <a name="prepare-linux-machines"></a>Préparer les machines Linux

1. Installez le service d’intégration pour Hyper-V Linux. Il est inclus par défaut dans la plupart des nouvelles versions des distributions Linux.
2. Regénérez l’image init Linux pour qu’elle contienne les pilotes Hyper-V nécessaires. Cela permet de s’assurer que la machine virtuelle démarrera dans Azure, et n’est nécessaire que sur certaines distributions.
3. [Activez la journalisation de la console série Azure](../virtual-machines/troubleshooting/serial-console-linux.md). Cela facilite la résolution des problèmes. Vous n’avez pas besoin de redémarrer la machine virtuelle. La machine virtuelle Azure démarre en utilisant l’image de disque. Cela équivaut à un redémarrage de la nouvelle machine virtuelle.
4. Mettez à jour le fichier de mappage d’appareil avec le nom d’appareil pour les associations de volumes de façon à utiliser des identificateurs d’appareil permanents.
5. Mettre à jour les entrées fstab pour utiliser des identificateurs de volume persistant.
6. Supprimez toutes les règles udev qui réservent des noms d’interface basés sur l’adresse MAC, etc.
7. Mettez à jour les interfaces réseau pour recevoir une adresse IP de DHCP.
8. [Apprenez-en davantage](../virtual-machines/linux/create-upload-generic.md) sur les étapes nécessaires à l’exécution d’une machine virtuelle Linux sur Azure et obtenez des instructions pour certaines distributions Linux très répandues.

## <a name="preserve-drive-letters-after-migration"></a>Conserver les lettres de lecteurs après la migration

Quand vous migrez un ordinateur local vers Microsoft Azure, les lettres de lecteurs de disques de données supplémentaires peuvent changer par rapport à leurs valeurs précédentes. Par défaut, les machines virtuelles Azure se voient affecter le lecteur D pour une utilisation comme stockage temporaire. À la suite de cette affectation de lecteur, tous les autres lecteurs de stockage attachés sont incrémentés d’une lettre.

Par exemple, si le lecteur de disque de données de votre installation utilise la lettre D pour les installations d’applications, celle-ci est incrémentée pour devenir le lecteur E après la migration de la machine virtuelle vers Azure. Pour empêcher cette attribution automatique et faire en sorte qu’Azure affecte la prochaine lettre de lecteur disponible à son volume temporaire, définissez la stratégie de réseau de zone de stockage (SAN) sur OnlineAll, comme ceci :

1. Sur l’ordinateur local (et non sur le serveur hôte), ouvrez une invite de commandes avec élévation de privilèges.
2. Tapez **diskpart**.
3. Tapez **SAN**. Si la lettre de lecteur du système d’exploitation invité n’est pas conservée, **Mettre hors connexion tous les disques** ou **Mettre hors connexion les disques partagés** est retourné.
4. À l’invite **DISKPART**, tapez **SANPOLICY=ONLINEALL**. Avec cette définition, vous êtes assuré que les disques sont mis en ligne et accessibles en lecture et écriture.
5. Pendant le test de migration, vous pouvez vérifier que les lettres de lecteurs sont conservées.


## <a name="check-azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les ordinateurs locaux que vous répliquez dans Azure doivent respecter les exigences des machines virtuelles Azure en ce qui concerne le système d’exploitation et l’architecture, les disques, les paramètres réseau et le nommage des machines virtuelles. Vérifiez les exigences concernant les [machines virtuelles/serveurs physiques VMware](migrate-support-matrix-vmware.md#azure-vm-requirements) et les [machines virtuelles Hyper-V](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) avant la migration.


## <a name="prepare-to-connect-after-migration"></a>Préparer la connexion après la migration

Des machines virtuelles Azure sont créées pendant la migration vers Azure. À l’issue de la migration, vous devez pouvoir vous connecter aux nouvelles machines virtuelles Azure. Pour cela, vous devez effectuer quelques étapes.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Préparer la connexion aux machines virtuelles Microsoft Azure

Sur les ordinateurs Windows locaux, procédez comme suit :

1. Configurez les paramètres Windows. Cela inclut la suppression des routes persistantes statiques ou du proxy WinHTTP.
2. Vérifiez que [ces services](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) sont en cours d’exécution.
3. Activez le bureau à distance (RDP) pour autoriser les connexions à distance à l’ordinateur local. [Découvrez comment](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) activer RDP avec PowerShell.
4. Pour accéder à une machine virtuelle Azure via Internet après la migration, dans le Pare-feu Windows de l’ordinateur local, autorisez TCP et UDP dans le profil public, puis définissez RDP comme application autorisée pour tous les profils.
5. Si vous voulez accéder à une machine virtuelle Azure via un réseau privé virtuel (VPN) de site à site après la migration, dans le Pare-feu Windows de l’ordinateur local, autorisez RDP pour les profils de domaine et privé. [Découvrez](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) comment autoriser le trafic RDP. 
6. Veillez à ce qu’aucune mise à jour Windows ne soit en attente sur la machine virtuelle locale pendant que vous migrez. Si c’est le cas, les mises à jour risquent de s’installer sur la machine virtuelle Azure après la migration, et vous ne pourrez pas vous y connecter tant que les mises à jour n’auront pas abouti.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Préparer la connexion aux machines virtuelles Azure Linux

Sur les ordinateurs Linux locaux, procédez comme suit :

1. Vérifiez que le service Secure Shell est configuré pour démarrer automatiquement au démarrage du système.
2. Vérifiez que les règles de pare-feu autorisent une connexion SSH.

### <a name="configure-azure-vms-after-migration"></a>Configurer les machines virtuelles Azure après la migration

Après la migration, effectuez les opérations suivantes sur les machines virtuelles Azure qui ont été créées.

1. Pour vous connecter à la machine virtuelle via Internet, attribuez-lui une adresse IP publique. Vous ne pouvez pas lui attribuer l’adresse IP publique que vous avez utilisée pour votre ordinateur local. [Plus d’informations](../virtual-network/virtual-network-public-ip-address.md)
2. Vérifiez que les règles de groupe de sécurité réseau (NSG) de la machine virtuelle autorisent les connexions entrantes sur le port RDP ou SSH.
3. Cochez [Diagnostics de démarrage](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) pour afficher la machine virtuelle.

> [!NOTE]
> Le service Azure Bastion offre un accès RDP et SSH privé aux machines virtuelles Azure. [Découvrez plus en détail](../bastion/bastion-overview.md) ce service.



## <a name="next-steps"></a>Étapes suivantes

Choisissez la méthode que vous souhaitez utiliser pour [migrer des machines virtuelles VMware](server-migrate-overview.md) sur Azure ou lancez la migration de [machines virtuelles Hyper-V](tutorial-migrate-hyper-v.md) ou de [serveurs physiques ou virtualisés/machines virtuelles cloud](tutorial-migrate-physical-virtual-machines.md).
