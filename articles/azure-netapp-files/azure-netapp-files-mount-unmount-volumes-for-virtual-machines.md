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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 4cd3bc7e4f95869d3efd2d92a7cdf1addc7ce5b2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953093"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Monter ou démonter un volume pour des machines virtuelles Windows ou Linux 

Vous pouvez monter ou démonter un volume pour des machines virtuelles Windows ou Linux selon les besoins.  Les instructions de montage pour les machines virtuelles Linux sont disponibles sur Azure NetApp Files.  

1. Cliquez sur le panneau **Volumes** et sélectionnez le volume pour lequel vous souhaitez effectuer un montage. 
2. Cliquez sur **Instructions de montage** à partir du volume sélectionné, puis suivez les instructions pour monter le volume. 

    ![Instructions de montage NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instructions de montage SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
Si vous utilisez NFSv4.1, utilisez la commande suivante pour monter votre système de fichiers :  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

## <a name="next-steps"></a>Étapes suivantes

* [Configurer le domaine par défaut NFSv4.1 pour Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* Consultez les [FAQ sur NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs) qui concernent la façon dont un volume NFS est automatiquement monté lors du démarrage ou du redémarrage d’une machine virtuelle Azure.
