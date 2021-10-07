---
title: Modifier l’option Masquer le chemin d’instantané d’Azure NetApp Files | Microsoft Docs
description: Décrit comment contrôler la visibilité d’un volume d’instantané avec Azure NetApp Files.
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
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 5b165bd2e4ca3ee6ac2d16bac7c4d5ff1117fdc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700536"
---
# <a name="edit-the-hide-snapshot-path-option-of-azure-netapp-files"></a>Modifier l’option Masquer le chemin d’instantané d’Azure NetApp Files
L’option Masquer le chemin d’instantané contrôle la visibilité du chemin d’accès de capture instantanée d’un volume. Lors de la création d’un volume [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) ou [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume), vous avez la possibilité de spécifier si le chemin d’accès de la capture instantanée doit être masqué. Vous pouvez ensuite modifier l’option Masquer le chemin d’instantané si nécessaire.  

> [!NOTE]
> Pour un [volume de destination](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) dans une réplication entre régions, l’option Masquer le chemin d’instantané est activée par défaut et ne peut pas être modifiée. 

## <a name="steps"></a>Étapes

1. Pour afficher le paramètre de l’option Masquer le chemin d’instantané d’un volume, sélectionnez celui-ci. Le champ **Masquer le chemin d’instantané** indique si l’option est activée.   
    ![Capture d’écran décrivant le champ Masquer le chemin d’instantané.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Pour modifier l’option Masquer le chemin d’instantané, cliquez sur **Modifier** dans la page du volume, puis modifiez l’option **Masquer le chemin d’instantané** si nécessaire.   
    ![Capture d’écran décrivant l’option Modifier la capture instantanée du volume.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les instantanés](snapshots-introduction.md)