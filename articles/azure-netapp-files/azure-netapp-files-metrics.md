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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848785"
---
# <a name="metrics-for-azure-netapp-files"></a>Métriques pour Azure NetApp Files

Azure NetApp Files fournit des métriques sur le stockage alloué, l'utilisation réelle du stockage, le débit du volume, les IOPS et la latence. En analysant ces métriques, vous en saurez plus sur le mode d'utilisation et les performances des volumes de vos comptes NetApp.  

## <a name="capacity_pools"></a>Métriques d'utilisation des pools de capacités

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Taille allouée utilisée du pool de volumes*  
    Quota total des volumes (en Gio) d’un pool de capacités donné (autrement dit, total des tailles provisionnées pour les volumes du pool de capacités)  
    Il s'agit de la taille que vous avez sélectionnée lors de la création des volumes.  
- *Taille logique totale du pool de volumes*  
    Espace logique total (en Gio) utilisé sur l’ensemble des volumes d’un pool de capacités  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Métriques d'utilisation des volumes

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Taille logique du volume*   
    Espace logique total utilisé dans un volume (en Gio)  
    Cette taille inclut l'espace logique utilisé par les captures instantanées et les systèmes de fichiers actifs.  
- *Taille d'instantané du volume*   
   Espace logique incrémentiel utilisé par les captures instantanées d’un volume  

## <a name="performance-metrics-for-volumes"></a>Métriques de performances des volumes

- *AverageReadLatency*   
    Temps moyen pour les lectures à partir du volume, en millisecondes
- *AverageWriteLatency*   
    Temps moyen pour les lectures à partir du volume, en millisecondes
- *ReadIops*   
    Nombre de lectures sur le volume par seconde
- *WriteIops*   
    Nombre d’écritures sur le volume par seconde

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
* [Créer un volume pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
