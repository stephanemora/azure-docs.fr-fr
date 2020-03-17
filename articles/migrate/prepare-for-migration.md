---
title: Préparer des machines pour la migration avec Azure Migrate
description: Découvrez comment préparer des machines locales pour la migration avec Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927473"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Préparer des ordinateurs locaux à une migration vers Azure

Cet article explique comment préparer des ordinateurs locaux avant de lancer leur migration vers Azure à l’aide d’[Azure Migrate : Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).

Dans cet article, vous découvrirez comment :
> [!div class="checklist"]
> * Vérifier les limitations de la migration.
> * Vérifier la configuration requise pour le système d’exploitation et les limites de prise en charge.
> * Examiner l’accès aux URL et aux ports pour les ordinateurs que vous voulez migrer.
> * Passer en revue les modifications que vous devrez peut-être apporter avant de commencer la migration.
> * Configurer les paramètres de telle sorte que les lettres de lecteurs soient conservées après la migration.
> * Préparer les ordinateurs de telle sorte que vous puissiez vous connecter aux machines virtuelles Azure après la migration.

## <a name="verify-migration-limitations"></a>Vérifier les limitations de la migration

- Vous pouvez évaluer jusqu’à 35 000 machines virtuelles VMware/Hyper-V dans un même projet Azure Migrate à l’aide de la fonctionnalité de migration de serveur Azure Migrate. Un projet peut inclure des machines virtuelles VMware et des machines virtuelles Hyper-V, dans les limites de chacune d’elles.
- Vous pouvez sélectionner jusqu’à 10 machines virtuelles à la fois pour la migration. Si vous devez en répliquer davantage, répliquez-les dans des groupes de 10.
- Pour la migration sans agent VMware, vous pouvez exécuter jusqu’à 100 réplications simultanément.

## <a name="verify-operating-system-requirements"></a>Vérifier la configuration requise pour le système d’exploitation

- Vérifiez que vos [systèmes d’exploitation Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) sont pris en charge dans Azure.
- Vérifiez que vos [distributions Linux](../virtual-machines/linux/endorsed-distros.md) sont prises en charge dans Azure.

## <a name="see-whats-supported"></a>Opérations prises en charge

Pour les machines virtuelles VMware, la migration de serveur prend en charge la [migration avec ou sans agent](server-migrate-overview.md).

- **Machines virtuelles VMware** : Vérifiez la [configuration requise et la prise en charge de la migration](migrate-support-matrix-vmware-migration.md) pour les machines virtuelles VMware.
- **Machines virtuelles Hyper-V** : Vérifiez la [configuration requise et la prise en charge pour la migration](migrate-support-matrix-hyper-v-migration.md) des machines virtuelles Hyper-V.
- **Machines physiques** : Vérifiez la [configuration requise et la prise en charge pour la migration](migrate-support-matrix-physical-migration.md) des ordinateurs physiques locaux et des autres serveurs virtualisés. 

## <a name="review-url-and-port-access"></a>Vérifier l’accès aux URL et aux ports

Les ordinateurs peuvent avoir besoin d’un accès Internet pendant la migration.

- **Appliance Azure Migrate** : Examinez les [URL](migrate-appliance.md#url-access) et les [ports](migrate-support-matrix-vmware-migration.md#agentless-ports) auxquels l’appliance Azure Migrate doit accéder pendant la migration sans agent.
- **Migration basée sur un agent de machines virtuelles VMware** : Examinez les [URL](migrate-replication-appliance.md#url-access) et les [ports](migrate-replication-appliance.md#port-access) que l’appliance de réplication utilise pendant la migration basée sur un agent de machines virtuelles VMware. 
- **Hôtes Hyper-V** : Examinez les [URL et les ports](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) auxquels les hôtes Hyper-V doivent accéder pendant la migration. 
- **Serveurs physiques** : Examinez les [URL](migrate-replication-appliance.md#url-access) et les [ports](migrate-replication-appliance.md#port-access) que l’appliance de réplication utilise pendant la migration de serveurs physiques.

## <a name="verify-required-changes-before-migrating"></a>Vérifier les modifications nécessaires avant la migration

Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure. Azure Migrate effectue automatiquement ces modifications pour les machines virtuelles qui exécutent ces systèmes exploitation :

- Red Hat Enterprise Linux 7.0+, 6.5+
- CentOS 7.0+, 6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

Pour les autres systèmes d’exploitation, vous devez préparer manuellement les machines avant la migration. 

### <a name="prepare-windows-machines"></a>Préparer les ordinateurs Windows

Si vous migrez une machine Windows, apportez les modifications suivantes avant la migration. Si vous migrez la machine virtuelle avant d’effectuer les modifications, elle risque de ne pas démarrer dans Azure.

1. [Activez la console série Azure](../virtual-machines/troubleshooting/serial-console-windows.md) pour la machine virtuelle Azure. L’activation de la console vous aide à résoudre les problèmes. Vous n’avez pas besoin de redémarrer la machine virtuelle. La machine virtuelle Azure démarre en utilisant l’image de disque. Le démarrage avec une image de disque équivaut à un redémarrage pour la nouvelle machine virtuelle. 
2. Si vous migrez des ordinateurs qui exécutent Windows Server 2003, installez les services d’intégration d’invité Hyper-V sur le système d’exploitation de la machine virtuelle. [Plus d’informations](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)

### <a name="prepare-linux-machines"></a>Préparer les ordinateurs Linux

1. Installez le service d’intégration pour Hyper-V Linux. La plupart des nouvelles versions des distributions Linux incluent par défaut les services d’intégration Hyper-V Linux.
2. Regénérez l’image init Linux pour qu’elle contienne les pilotes Hyper-V nécessaires. La regénération de l’image init est l’assurance que la machine virtuelle démarrera dans Azure (nécessaire uniquement sur certaines distributions).
3. [Activez la journalisation de la console série Azure](../virtual-machines/troubleshooting/serial-console-linux.md). L’activation de la journalisation de la console vous aide à résoudre les problèmes. Vous n’avez pas besoin de redémarrer la machine virtuelle. La machine virtuelle Azure démarre en utilisant l’image de disque. Le démarrage avec une image de disque équivaut à un redémarrage pour la nouvelle machine virtuelle.
4. Mettez à jour le fichier de mappage d’appareils avec les associations entre les volumes et les noms d’appareils de façon à utiliser des identificateurs d’appareils persistants.
5. Mettez à jour les entrées dans le fichier fstab pour utiliser des identificateurs de volumes persistants.
6. Supprimez les règles udev qui réservent des noms d’interface basés sur l’adresse MAC, etc.
7. Mettez à jour les interfaces réseau pour recevoir une adresse IP de DHCP.

Apprenez-en davantage sur les [étapes nécessaires à l’exécution d’une machine virtuelle Linux sur Azure](../virtual-machines/linux/create-upload-generic.md) et obtenez des instructions pour certaines distributions Linux très répandues.

## <a name="preserve-drive-letters-after-migration"></a>Conserver les lettres de lecteurs après la migration

Quand vous migrez un ordinateur local vers Microsoft Azure, les lettres de lecteur des disques de données supplémentaires peuvent changer par rapport à leurs valeurs d’origine. 

Par défaut, les machines virtuelles Azure se voient affecter le lecteur D pour une utilisation comme stockage temporaire. À la suite de cette affectation de lecteur, tous les autres lecteurs de stockage attachés sont incrémentés d’une lettre. Par exemple, si le lecteur de disque de données de votre installation utilise la lettre D pour les installations d’applications, celle-ci est incrémentée pour devenir le lecteur E après la migration de la machine virtuelle vers Azure. Pour empêcher cette affectation automatique et faire en sorte qu’Azure affecte la prochaine lettre de lecteur disponible à son volume temporaire, définissez la stratégie de réseau de zone de stockage (SAN) sur **OnlineAll** :

1. Sur l’ordinateur local (pas sur le serveur hôte), ouvrez une invite de commandes avec élévation de privilèges.
2. Entrez **diskpart**.
3. Entrez **SAN**. Si la lettre de lecteur du système d’exploitation invité n’est pas conservée, **Mettre hors connexion tous les disques** ou **Mettre hors connexion les disques partagés** est retourné.
4. À l’invite **DISKPART**, entrez **SAN Policy=OnlineAll**. Ce paramètre est l’assurance que les disques sont mis en ligne et que vous pouvez lire et écrire sur les deux disques.
5. Pendant le test de migration, vous pouvez vérifier que les lettres de lecteurs sont conservées.

## <a name="check-azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les ordinateurs locaux que vous répliquez dans Azure doivent être conformes la configuration requise des machines virtuelles Azure en ce qui concerne le système d’exploitation et l’architecture, les disques, les paramètres réseau et le nommage des machines virtuelles. Vérifiez la configuration requise pour les [machines virtuelles et les serveurs physiques VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) et les [machines virtuelles Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) avant la migration.

## <a name="prepare-to-connect-after-migration"></a>Préparer la connexion après la migration

Des machines virtuelles Azure sont créées pendant la migration vers Azure. Après la migration, vous devez pouvoir vous connecter aux nouvelles machines virtuelles Azure. Plusieurs étapes sont nécessaires pour établir une connexion.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Préparer la connexion aux machines virtuelles Windows Azure

Sur des ordinateurs Windows locaux :

1. Configurez les paramètres Windows. Parmi les paramètres figurent la suppression des routes persistantes statiques ou du proxy WinHTTP.
2. Vérifiez que les [services nécessaires](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) sont en cours d’exécution.
3. Activez le bureau à distance (RDP) pour autoriser les connexions à distance à l’ordinateur local. Découvrez comment [utiliser PowerShell pour activer RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Pour accéder à une machine virtuelle Azure via Internet après la migration, dans le Pare-feu Windows de l’ordinateur local, autorisez TCP et UDP dans le profil public, puis définissez RDP comme application autorisée pour tous les profils.
5. Si vous voulez accéder à une machine virtuelle Azure via un réseau privé virtuel (VPN) de site à site après la migration, dans le Pare-feu Windows de l’ordinateur local, autorisez RDP pour les profils de domaine et privé. Découvrez comment [autoriser le trafic RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Veillez à ce qu’aucune mise à jour Windows ne soit en attente sur la machine virtuelle locale pendant la migration. Si c’est le cas, les mises à jour risquent de s’installer sur la machine virtuelle Azure après la migration, et vous ne pourrez pas vous y connecter tant que les mises à jour n’auront pas abouti.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Préparer la connexion aux machines virtuelles Azure Linux

Sur les ordinateurs Linux locaux :

1. Vérifiez que le service Secure Shell est configuré pour démarrer automatiquement au démarrage du système.
2. Vérifiez que les règles de pare-feu autorisent une connexion SSH.

### <a name="configure-azure-vms-after-migration"></a>Configurer les machines virtuelles Azure après la migration

Après la migration, effectuez ces étapes sur les machines virtuelles Azure qui ont été créées :

1. Pour vous connecter à la machine virtuelle via Internet, attribuez-lui une adresse IP publique. Pour la machine virtuelle Azure, vous devez utiliser une adresse IP publique différente de celle que vous avez utilisée pour votre ordinateur local. [Plus d’informations](../virtual-network/virtual-network-public-ip-address.md)
2. Vérifiez que les règles de groupe de sécurité réseau (NSG) de la machine virtuelle autorisent les connexions entrantes sur le port RDP ou SSH.
3. Cochez [diagnostics de démarrage](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) pour afficher la machine virtuelle.

> [!NOTE]
> Le service Azure Bastion offre un accès RDP et SSH privé aux machines virtuelles Azure. [Découvrez plus en détail](../bastion/bastion-overview.md) ce service.

## <a name="next-steps"></a>Étapes suivantes

Choisissez la méthode que vous souhaitez utiliser pour [migrer des machines virtuelles VMware](server-migrate-overview.md) vers Azure, ou lancez la migration des [machines virtuelles Hyper-V](tutorial-migrate-hyper-v.md) ou des [serveurs physiques ou virtualisés ou des machines virtuelles cloud](tutorial-migrate-physical-virtual-machines.md).
