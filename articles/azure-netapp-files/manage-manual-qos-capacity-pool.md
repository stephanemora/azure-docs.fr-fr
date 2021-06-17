---
title: Gérer un pool de capacités avec QoS manuelle pour Azure NetApp Files | Microsoft Docs
description: Décrit comment gérer un pool de capacités qui utilise le type QoS manuelle, notamment la configuration d’un pool de capacités avec QoS manuelle et la modification d’un pool de capacités pour utiliser la Qualité de service manuelle.
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 18333b7c9fded73d330cacd6322b0869bf65f9d9
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071080"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Gérer un pool de capacités de QoS manuel

Cet article explique comment gérer un pool de capacités qui utilise le type QoS manuelle.  

Pour comprendre les considérations relatives aux types de QoS, consultez [Hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) et [Considérations relatives aux performances pour Azure NetApp Files](azure-netapp-files-performance-considerations.md).  

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Configurer un nouveau pool de capacités avec QoS manuelle 

Pour créer un pool de capacités qui utilise le type QoS manuelle :

1. Suivez les étapes décrites dans [Configurer un pool de capacités](azure-netapp-files-set-up-capacity-pool.md).  

2. Dans la fenêtre Nouveau pool de capacités, sélectionnez le type **QoS manuelle**.  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Modifier un pool de capacités pour utiliser la QoS manuelle

Vous pouvez modifier un pool de capacités qui utilise actuellement le type QoS automatique pour utiliser le type QoS manuelle.  

> [!IMPORTANT]
> La définition du type de capacité sur la QoS manuelle est une modification permanente. Vous ne pouvez pas convertir un outil de capacité de type QoS manuelle en pool de capacités avec QoS automatique.  
> Au moment de la conversion, les niveaux de débit peuvent être plafonnés pour respecter les limites de débit pour les volumes de type QoS manuelle. Voir [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits).

1. Dans le panneau de gestion de votre compte NetApp, cliquez sur **Pools de capacités** pour afficher les pools de capacités existants.   
 
2.  Cliquez sur le pool de capacités que vous souhaitez modifier pour utiliser la QoS manuelle.

3.  Cliquez sur **Changer de type QoS**. Définissez ensuite **Nouveau type QoS** sur **Manuelle**. Cliquez sur **OK**. 

![Changer de type QoS](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Analyser le débit d’un pool de capacités avec QoS manuelle  

Des métriques sont disponibles pour vous aider à analyser le débit de lecture et d’écriture d’un volume.  Voir [Métriques pour Azure NetApp Files](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Modifier le débit alloué d’un volume avec QoS manuelle 

Si un volume est contenu dans un pool de capacités avec QoS manuelle, vous pouvez modifier le débit de volume alloué en fonction des besoins.

1. Sur la page **Volumes**, sélectionnez le volume dont vous souhaitez modifier le débit.   

2. Cliquez sur **Modifier le débit**. Spécifiez le **débit (Mio/s)** souhaité. Cliquez sur **OK**. 

    ![Modifier le débit de QoS](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Configurer un pool de capacité](azure-netapp-files-set-up-capacity-pool.md)
* [Métriques pour Azure NetApp Files](azure-netapp-files-metrics.md)
* [Considérations sur les performances pour Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Résoudre les problèmes de pools de capacités](troubleshoot-capacity-pools.md)
* [Hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Créer un volume NFS](azure-netapp-files-create-volumes.md)
* [Créer un volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Créer un volume à deux protocoles](create-volumes-dual-protocol.md)
