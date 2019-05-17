---
title: Questions fréquentes sur les fichiers NetApp Azure | Microsoft Docs
description: Réponses aux questions fréquentes sur Azure Files de NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806385"
---
# <a name="faqs-about-azure-netapp-files"></a>Questions fréquentes sur les fichiers NetApp Azure

Cet article répond aux questions fréquemment posées (FAQ) sur Azure Files de NetApp. 

## <a name="networking-faqs"></a>FAQ sur la mise en réseau

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Le chemin d’accès de données NFS passe par Internet ?  

Non. Le chemin d’accès de données NFS ne passe pas par Internet. Azure Files de NetApp est un service natif Azure qui est déployé dans le réseau virtuel de Azure (VNet) dans lequel le service est disponible. Fichiers NetApp Azure utilise un sous-réseau de délégué et configure une interface réseau directement sur le réseau virtuel. 

Consultez [les instructions pour les fichiers de NetApp Azure réseau planification](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) pour plus d’informations.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Puis-je connecter un réseau virtuel que j’ai déjà créé pour le service de fichiers NetApp de Azure ?

Oui, vous pouvez connecter des réseaux virtuels que vous avez créé pour le service. 

Consultez [les instructions pour les fichiers de NetApp Azure réseau planification](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) pour plus d’informations.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Puis-je monter un volume NFS de fichiers NetApp de Azure à l’aide du nom FQDN de DNS ?

Oui, vous pouvez, si vous créez les entrées DNS requises. Les fichiers NetApp Azure fournit l’adresse IP de service pour le volume configuré. 

> [!NOTE] 
> Les fichiers NetApp Azure peuvent déployer des adresses IP supplémentaires pour le service en fonction des besoins.  Les entrées DNS peut-être être mis à jour régulièrement.

## <a name="security-faqs"></a>Forum aux questions sur la sécurité

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Le trafic réseau entre la machine virtuelle Azure et le stockage peut être chiffré ?

Le trafic de données (le trafic à partir du client NFSv3 ou smbv3 fournis par les volumes de fichiers NetApp de Azure) n’est pas chiffré. Toutefois, le trafic à partir d’une machine virtuelle Azure (en cours d’exécution un client SMB ou NFS) vers Azure Files de NetApp est aussi sécurisé que tout autre trafic Azure-VM-VM. Ce trafic est local pour le réseau de centre de données Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Le stockage peut être chiffré au repos ?

Tous les volumes de fichiers NetApp de Azure sont chiffrées à l’aide de la norme FIPS 140-2 standard. Toutes les clés sont gérées par le service de fichiers NetApp de Azure. 

### <a name="how-are-encryption-keys-managed"></a>La gestion des clés de chiffrement ? 

Gestion de clés pour les fichiers de NetApp Azure est gérée par le service.  Actuellement, les clés gérées par l’utilisateur (apportez votre propre clés) ne sont pas prises en charge.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Puis-je configurer les règles de stratégie d’exportation NFS pour contrôler l’accès à la cible de montage du service Azure NetApp Files ?


Oui, vous pouvez configurer jusqu'à cinq règles dans une seule stratégie d’exportation NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Fichiers de NetApp Azure prend en charge les groupes de sécurité réseau ?

Non, actuellement vous ne pouvez pas appliquer des groupes de sécurité réseau au sous-réseau délégué de fichiers NetApp de Azure ou les interfaces réseau créés par le service.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Puis-je utiliser Azure IAM avec Azure NetApp Files ?

Oui, les fichiers NetApp Azure prend en charge les fonctionnalités RBAC avec Azure IAM.

## <a name="performance-faqs"></a>Forum aux questions de performances

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Que dois-je faire pour optimiser ou régler les performances de fichiers NetApp de Azure ?

Vous pouvez effectuer les actions suivantes par les exigences de performances : 
- Assurez-vous que la taille de la Machine virtuelle est en conséquence.
- Activer la mise en réseau accélérée pour la machine virtuelle.
- Sélectionnez le niveau de service souhaité et la taille pour le pool de capacité.
- Créer un volume avec la taille de quota de votre choix pour la capacité et les performances.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Comment convertir des niveaux de service en fonction du débit d’Azure Files de NetApp e/s ?

Vous pouvez convertir Mo/s pour les e/s à l’aide de la formule suivante :  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Comment modifier le niveau de service d’un volume ?

Modification du niveau de service d’un volume n’est pas pris en charge actuellement.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Comment surveiller les performances des fichiers de NetApp Azure ?

Fichiers NetApp Azure fournit des métriques de performances de volume. Vous pouvez également utiliser Azure Monitor pour surveiller les métriques de l’utilisation pour Azure Files de NetApp.  Consultez [métriques pour Azure Files de NetApp](azure-netapp-files-metrics.md) pour obtenir la liste de mesures de performances pour Azure Files de NetApp.

## <a name="nfs-faqs"></a>Forum aux questions sur NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Je souhaite disposer d’un volume monté automatiquement lorsqu’une machine virtuelle Azure est démarrée ou redémarrée.  Comment configurer mon hôte pour les volumes NFS persistants ?

Pour un volume NFS automatiquement monté au moment du démarrage de la machine virtuelle ou d’un redémarrage, ajoutez une entrée à la `/etc/fstab` fichier sur l’hôte. 

Par exemple : `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    L’adresse IP du volume Azure Files de NetApp trouvé dans le volet de propriétés de volume
- $FILEPATH  
    Le chemin d’exportation du volume de fichiers NetApp de Azure
- $MOUNTPOINT  
    Le répertoire créé sur l’hôte Linux utilisé pour monter l’exportation NFS

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Pourquoi la commande DF sur le client NFS pas ne pas afficher la taille du volume approvisionné ?

La taille du volume signalée dans DF est la taille maximale que peut atteindre le volume de fichiers NetApp de Azure. La taille du volume Azure Files de NetApp dans commande DF n’est pas de quota ou de la taille du volume.  Vous pouvez obtenir la taille du volume Azure Files de NetApp ou le quota par le biais du portail Azure ou l’API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Quelle version NFS Azure NetApp Files prend en charge ?

Les fichiers NetApp Azure prend actuellement en charge NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Comment activer la racine débogage ?

Débogage de racine n’est actuellement pas pris en charge.

## <a name="smb-faqs"></a>Forum aux questions sur SMB

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Fichiers de NetApp Azure prend en charge Azure Active Directory ?

Non, il est actuellement pas pris en charge.  Les fichiers NetApp Azure prend en charge Active Directory Domain Services (apportez votre propre AD), qui peut utiliser des contrôleurs de domaine Active Directory existants avec Azure Files de NetApp. Contrôleurs de domaine peuvent résider dans Azure en tant que machines virtuelles ou en local via ExpressRoute.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Est une connexion Active Directory requise pour un accès SMB ? 

Oui, vous devez créer une connexion Active Directory avant de déployer un volume SMB. Les contrôleurs de domaine spécifié doit être accessibles par le sous-réseau délégué d’Azure Files de NetApp pour une connexion réussie.  Consultez [créer un volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) pour plus d’informations. 

### <a name="how-many-active-directory-connections-are-supported"></a>Le nombre de connexions Active Directory est pris en charge ?

Les fichiers NetApp Azure prend actuellement en charge une seule connexion Active Directory par abonnement. En outre, la connexion Active Directory est spécifique à un seul compte NetApp ; Il n’est pas partagé entre les comptes. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Quelles versions de Windows Active Directory sont pris en charge ?

Fichiers NetApp Azure prend en charge la version 2016 de 2008r2SP1 de Windows Server des Services de domaine Active Directory.

## <a name="capacity-management-faqs"></a>FAQ sur la gestion de la capacité

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Comment surveiller l’utilisation de pool de capacité et le volume de fichiers NetApp de Azure ? 

Fichiers NetApp Azure fournit des métriques de capacité pool et le volume d’utilisation. Vous pouvez également utiliser Azure Monitor pour surveiller l’utilisation de fichiers NetApp de Azure. Consultez [métriques pour Azure Files de NetApp](azure-netapp-files-metrics.md) pour plus d’informations. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Puis-je gérer Azure Files de NetApp via l’Explorateur de stockage Azure ?

Non. Les fichiers NetApp Azure n’est pas prise en charge par l’Explorateur de stockage Azure.

## <a name="data-migration-and-protection-faqs"></a>FAQ de migration et la protection des données

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Comment migrer des données dans des fichiers de NetApp Azure ?
Azure Files de NetApp fournit les volumes NFS et SMB.  Vous pouvez utiliser n’importe quel outil de copie basée sur le fichier pour migrer des données au service. 

NetApp offre une solution SaaS, [synchronisation du Cloud NetApp](https://cloud.netapp.com/cloud-sync-service).  La solution vous permet de répliquer NFS ou le partage des données SMB pour les exportations NFS de fichiers NetApp Azure ou SMB. 

Vous pouvez également utiliser un large éventail d’outils gratuits pour copier des données. Pour NFS, vous pouvez utiliser les outils de charges de travail, tel que [rsync](https://rsync.samba.org/examples.html) pour copier et synchroniser les données sources dans un volume Azure Files de NetApp. Pour SMB, vous pouvez utiliser des charges de travail [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) de la même manière.  Ces outils peuvent également répliquer les autorisations de fichier ou dossier. 

La configuration requise pour la migration de données en local vers Azure Files de NetApp est les suivantes : 

- Vérifiez que les fichiers NetApp Azure est disponible dans la région Azure cible.
- Valider la connectivité réseau entre la source et l’adresse IP de volume Azure Files de NetApp cible. Transfert de données entre sur site et le service de fichiers NetApp de Azure est pris en charge via ExpressRoute.
- Créer la cible de volume Azure Files de NetApp.
- Transférer les données de la source vers le volume cible à l’aide de votre outil de copie de fichier par défaut.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Comment créer une copie d’un volume Azure Files de NetApp dans une autre région Azure ?
    
Azure Files de NetApp fournit les volumes NFS et SMB.  N’importe quel outil de copie en fonction du fichier peut être utilisé pour répliquer des données entre des régions Azure. 

NetApp offre une solution SaaS basé, [synchronisation du Cloud NetApp](https://cloud.netapp.com/cloud-sync-service).  La solution vous permet de répliquer NFS ou le partage des données SMB pour les exportations NFS de fichiers NetApp Azure ou SMB. 

Vous pouvez également utiliser un large éventail d’outils gratuits pour copier des données. Pour NFS, vous pouvez utiliser les outils de charges de travail, tel que [rsync](https://rsync.samba.org/examples.html) pour copier et synchroniser les données sources dans un volume Azure Files de NetApp. Pour SMB, vous pouvez utiliser des charges de travail [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) de la même manière.  Ces outils peuvent également répliquer les autorisations de fichier ou dossier. 

La configuration requise pour la réplication d’un volume Azure Files de NetApp vers une autre région Azure est les suivantes : 
- Vérifiez que les fichiers NetApp Azure est disponible dans la région Azure cible.
- Valider la connectivité réseau entre des réseaux virtuels dans chaque région. Actuellement, l’homologation globale entre réseaux virtuels n’est pas pris en charge.  Vous pouvez établir la connectivité entre réseaux virtuels en le liant à un circuit ExpressRoute ou à l’aide d’une connexion S2S VPN. 
- Créer la cible de volume Azure Files de NetApp.
- Transférer les données de la source vers le volume cible à l’aide de votre outil de copie de fichier par défaut.

### <a name="is-migration-with-azure-data-box-supported"></a>Est la migration avec Azure Data Box est pris en charge ?

Non. Azure Data Box ne prend pas en charge les fichiers de NetApp Azure actuellement. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Est la migration avec le service Azure Import/Export pris en charge ?

Non. Service Azure Import/Export ne prend pas en charge les fichiers de NetApp Azure actuellement.

## <a name="next-steps"></a>Étapes suivantes  

- [Forum aux questions sur Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [FAQ sur le réseau virtuel Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)