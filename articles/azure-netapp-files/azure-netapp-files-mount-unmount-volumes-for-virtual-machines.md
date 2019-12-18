---
title: Monter ou démonter un volume Azure NetApp Files pour des machines virtuelles Windows ou Linux | Microsoft Docs
description: Décrit comment monter ou démonter un volume pour des machines virtuelles Windows ou Linux.
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
ms.date: 12/05/2019
ms.author: b-juche
ms.openlocfilehash: 7f1e9500a9268e0fba054f7065e858cd801aca7b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894094"
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
