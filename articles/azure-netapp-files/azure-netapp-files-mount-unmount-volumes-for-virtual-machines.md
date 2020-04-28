---
title: Monter des volumes Azure NetApp Files pour des machines virtuelles
description: Découvrez comment monter ou démonter un volume pour des machines virtuelles Windows ou Linux dans Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084938"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Monter ou démonter un volume pour des machines virtuelles Windows ou Linux 

Vous pouvez monter ou démonter un volume pour des machines virtuelles Windows ou Linux selon les besoins.  Les instructions de montage pour les machines virtuelles Linux sont disponibles sur Azure NetApp Files.  

1. Cliquez sur le panneau **Volumes** et sélectionnez le volume pour lequel vous souhaitez effectuer un montage. 
2. Cliquez sur **Instructions de montage** à partir du volume sélectionné, puis suivez les instructions pour monter le volume. 

    ![Instructions de montage NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instructions de montage SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Si vous utilisez NFSv4.1, utilisez la commande suivante pour monter votre système de fichiers : `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Si vous souhaitez qu’un volume NFS soit automatiquement monté lors du démarrage ou du redémarrage d’une machine virtuelle Azure ajoutez une entrée au fichier `/etc/fstab` sur l’hôte. 

    Par exemple : `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` est l’adresse IP du volume Azure NetApp Files se trouvant dans le panneau Propriétés de volume.
    * `$FILEPATH` est le chemin d’exportation du volume Azure NetApp Files.
    * `$MOUNTPOINT`est le répertoire créé sur l’hôte Linux utilisé pour monter l’exportation NFS.

4. Si vous souhaitez monter le volume sur Windows en utilisant NFS, procédez comme suit :

    a. Montez d’abord le volume sur une machine virtuelle Unix ou Linux.  
    b. Exécutez une commande `chmod 777` ou `chmod 775` sur le volume.  
    c. Montez le volume par le biais du client NFS sur Windows.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer le domaine par défaut NFSv4.1 pour Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [FAQ relatifs au NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Vue d’ensemble du système de fichiers NFS](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
