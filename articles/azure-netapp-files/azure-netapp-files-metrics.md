---
title: Métriques pour Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files fournit des métriques sur le stockage alloué, l’utilisation réelle du stockage, les IOPS du volume et la latence. Utilisez ces métriques pour comprendre l’utilisation et les performances.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 7e6ab90010d4379c1640f73a8deeba874e601daf
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513806"
---
# <a name="metrics-for-azure-netapp-files"></a>Métriques pour Azure NetApp Files

Azure NetApp Files fournit des métriques sur le stockage alloué, l’utilisation réelle du stockage, les IOPS du volume et la latence. En analysant ces métriques, vous en saurez plus sur le mode d'utilisation et les performances des volumes de vos comptes NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métriques d'utilisation des pools de capacités

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pool alloué à la taille du volume*  
    Quota total des volumes (en Gio) d’un pool de capacités donné (autrement dit, total des tailles provisionnées pour les volumes du pool de capacités).  
    Il s’agit de la taille que vous avez sélectionnée lors de la création des volumes.  
- *Taille du pool consommée*  
    Espace logique total (en Gio) utilisé sur l’ensemble des volumes d’un pool de capacités.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Métriques d'utilisation des volumes

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
-  *Taille du volume consommée*  
    Espace logique total utilisé dans un volume (en Gio).  
    Cette taille inclut l'espace logique utilisé par les captures instantanées et les systèmes de fichiers actifs.  
- *Taille du cliché instantané de volume*   
   Espace logique incrémentiel utilisé par les clichés instantanés d’un volume.  

## <a name="performance-metrics-for-volumes"></a>Métriques de performances des volumes

- *AverageReadLatency*   
    Temps moyen des lectures du volume (en millisecondes).
- *AverageWriteLatency*   
    Temps moyen des écritures du volume (en millisecondes).
- *ReadIops*   
    Nombre de lectures sur le volume par seconde.
- *WriteIops*   
    Nombre d’écritures sur le volume par seconde.

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
* [Créer un volume pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
