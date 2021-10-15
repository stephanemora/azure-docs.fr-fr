---
title: Gérer la reprise d’activité à l’aide de la réplication inter-région Azure NetApp Files | Microsoft Docs
description: Décrit comment gérer la reprise d’activité après sinistre à l’aide de la réplication inter-région Azure NetApp Files.
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
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: 4f1f766fbec4c9e09d1ebd5e982254cdcbd85403
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273322"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Gérer la reprise d’activité à l’aide de la réplication inter-région 

Une réplication continue entre les volumes source et de destination (voir [Créer une réplication de volume](cross-region-replication-create-peering.md)) vous prépare en vue d’un événement de reprise d’activité après sinistre. 

Lorsqu’un tel événement se produit, vous pouvez [basculer vers le volume de destination](#fail-over-to-destination-volume), ce qui permet au client de lire et d’écrire sur ce volume. 

Après la reprise d’activité, vous pouvez effectuer une opération de [resynchronisation](#resync-replication) pour basculer vers volume source. Ensuite, vous [rétablissez la réplication de la source vers la destination](#reestablish-source-to-destination-replication) et remontez le volume source pour que le client puisse y accéder. 

Les détails sont décrits ci-dessous. 

## <a name="fail-over-to-destination-volume"></a>Basculer vers le volume de destination

Lorsque vous devez activer le volume de destination (par exemple lorsque vous souhaitez basculer vers la région de destination), vous devez arrêter le peering de réplication, puis monter le volume de destination.  

1. Pour arrêter le peering de réplication, sélectionnez le volume de destination. Cliquez sur **Réplication** sous Service de stockage.  

2.  Vérifiez les champs suivants avant de continuer :  
    * Vérifiez qu’État du miroir indique ***En miroir***.   
        N’essayez pas d’arrêter le peering de réplication si État du miroir indique *Non initialisé*.
    * Vérifiez qu’État de la relation indique ***Inactive***.   
        N’essayez pas d’arrêter le peering de réplication si État de la relation indique *Transfert en cours*.   

    Voir [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md). 

3.  Cliquez sur **Arrêter l’appairage**.  

4.  Tapez **Oui** quand vous y êtes invité, puis cliquez sur le bouton **Arrêter**. 

    ![Arrêter le peering de réplication](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Montez le volume de destination en suivant les étapes décrites dans [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Cette étape permet à un client d’accéder au volume de destination.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>Resynchroniser les volumes après une récupération d’urgence

Après la reprise d’activité, vous pouvez réactiver le volume source en effectuant une opération de resynchronisation.  L’opération de resynchronisation inverse le processus de réplication et synchronise les données du volume de destination vers le volume source.  

> [!IMPORTANT] 
> L’opération de resynchronisation synchronise les volumes source et de destination en mettant à jour de façon incrémentielle le volume source avec les dernières mises à jour à partir du volume de destination, en fonction des dernières captures instantanées courantes disponibles. Cette opération évite de devoir synchroniser l’ensemble du volume dans la plupart des cas, car seules les modifications apportées au volume de destination *après* la capture instantanée commune la plus récente vont devoir être répliquées sur le volume source.  
> 
> L’opération de resynchronisation remplace toutes les données plus récentes (par rapport à la capture instantanée commune la plus courante) du volume source par les données du volume de destination mises à jour. L’interface utilisateur vous avertit du risque de perte de données. Vous êtes invité à confirmer l’action de resynchronisation avant le démarrage de l’opération.  
> 
> Si le volume source n’a pas survécu à l’incident et qu’il n’existe donc aucune capture instantanée commune, toutes les données de la destination sont resynchronisées avec un volume source nouvellement créé.


1. Pour resynchroniser la réplication, sélectionnez le volume *source*. Cliquez sur **Réplication** sous Service de stockage. Cliquez ensuite sur **Resynchroniser**.  

2. Tapez **Oui** quand vous y êtes invité, puis cliquez sur **Resynchroniser**. 
 
    ![Resynchroniser la réplication](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Supervisez l’état d’intégrité du volume source en suivant les étapes décrites dans [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md).   
    Lorsque l’état d’intégrité du volume source affiche les valeurs suivantes, l’opération de resynchronisation est terminée et les modifications apportées sur le volume de destination sont maintenant capturées sur le volume source :   

    * État de la mise en miroir : *En miroir*  
    * État du transfert : *Idle*  

## <a name="reestablish-source-to-destination-replication"></a>Rétablir la réplication de la source vers la destination

Une fois l’opération de resynchronisation de la destination vers la source terminée, vous devez à nouveau arrêter le peering de réplication afin de rétablir la réplication de la source vers la destination. Vous devez également remonter le volume source afin que le client puisse y accéder.  

1. Arrêtez le peering de réplication :  
    a. Sélectionnez le volume de *destination*. Cliquez sur **Réplication** sous Service de stockage.  
    b. Vérifiez les champs suivants avant de continuer :   
    * Vérifiez qu’État du miroir indique ***En miroir***.   
    N’essayez pas d’arrêter le peering de réplication si État du miroir indique *Non initialisé*.  
    * Vérifiez qu’État de la relation indique ***Inactive***.   
    N’essayez pas d’arrêter le peering de réplication si État de la relation indique *Transfert en cours*.    

        Voir [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md). 

    c. Cliquez sur **Arrêter l’appairage**.   
    d. Tapez **Oui** quand vous y êtes invité, puis cliquez sur le bouton **Arrêter**.  

2. Resynchronisez le volume source avec le volume de destination :  
    a. Sélectionnez le volume de *destination*. Cliquez sur **Réplication** sous Service de stockage. Cliquez ensuite sur **Resynchroniser**.   
    b. Tapez **Oui** quand vous y êtes invité, puis cliquez sur le bouton **Resynchroniser**.

3. Remontez le volume source en suivant les étapes décrites dans [Monter ou démonter un volume pour des machines virtuelles Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Cette étape permet à un client d’accéder au volume source.

## <a name="next-steps"></a>Étapes suivantes  

* [Réplication entre régions](cross-region-replication-introduction.md)
* [Configuration requise et considérations pour la réplication entre régions](cross-region-replication-requirements-considerations.md)
* [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md)
* [Redimensionner un volume de destination de réplication entre régions](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Métriques de réplication de volume](azure-netapp-files-metrics.md#replication)
* [Supprimer des volumes ou des réplications de volume](cross-region-replication-delete.md)
* [Résoudre les problèmes de réplication inter-région](troubleshoot-cross-region-replication.md)

