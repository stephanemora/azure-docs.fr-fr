---
title: Métriques pour Azure NetApp Files | Microsoft Docs
description: Décrit les métriques relatives à Azure NetApp Files.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61084937"
---
# <a name="metrics-for-azure-netapp-files"></a>Métriques pour Azure NetApp Files

Azure NetApp Files fournit des métriques sur le stockage alloué, l'utilisation réelle du stockage, le débit du volume, les IOPS et la latence. En analysant ces métriques, vous en saurez plus sur le mode d'utilisation et les performances des volumes de vos comptes NetApp.  

## <a name="capacity_pools"></a>Métriques d'utilisation des pools de capacités

- *Taille allouée au pool de volumes*  
    Taille (en Gio) du pool de capacités approvisionné.  
- *Taille allouée utilisée du pool de volumes*  
    Quota total des volumes (en Gio) d'un pool de capacités donné (autrement dit, total des tailles approvisionnées pour les volumes du pool de capacités). Il s'agit de la taille que vous avez sélectionnée lors de la création des volumes.  
- *Taille logique totale du pool de volumes*  
    Espace logique total (en Gio) utilisé sur l'ensemble des volumes d'un pool de capacités.  
- *Taille d'instantané totale du pool de volumes*  
    Espace logique incrémentiel total utilisé par les captures instantanées.  

## <a name="volumes"></a>Métriques d'utilisation des volumes

- *Taille allouée aux volumes*   
    Taille des volumes (quota) approvisionnée, en Gio.  
- *Taille logique du volume*   
    Espace logique total utilisé dans un volume (en Gio). Cette taille inclut l'espace logique utilisé par les captures instantanées et les systèmes de fichiers actifs.  
- *Taille d'instantané du volume*   
    Espace logique incrémentiel utilisé par les captures instantanées d'un volume.  

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
* [Créer un volume pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
