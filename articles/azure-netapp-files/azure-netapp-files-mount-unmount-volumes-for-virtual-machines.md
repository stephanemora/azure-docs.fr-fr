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
ms.date: 03/07/2019
ms.author: b-juche
ms.openlocfilehash: 76e01055043932f2c7e7d57bd7eed6265d666a8c
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302772"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Monter ou démonter un volume pour des machines virtuelles Windows ou Linux 

Vous pouvez monter ou démonter un volume pour des machines virtuelles Windows ou Linux selon les besoins.  Les instructions de montage pour les machines virtuelles Linux sont disponibles sur Azure NetApp Files.  

1. Cliquez sur le panneau **Volumes** et sélectionnez le volume pour lequel vous souhaitez effectuer un montage. 
2. Cliquez sur **Instructions de montage** à partir du volume sélectionné, puis suivez les instructions pour monter le volume. 

    ![Instructions de montage NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instructions de montage SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
Si vous utilisez NFSv 4.1, utilisez la commande suivante pour monter votre système de fichiers : `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`
