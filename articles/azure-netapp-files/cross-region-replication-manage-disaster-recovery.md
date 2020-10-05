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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708314"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Gérer la reprise d’activité à l’aide de la réplication inter-région 

Une réplication continue entre les volumes source et de destination (voir [Créer un peering de réplication](cross-region-replication-create-peering.md)) vous prépare en vue d’un événement de reprise d’activité après sinistre. 

Lorsqu’un tel événement se produit, vous pouvez [basculer vers le volume de destination](#break-replication-peering-to-activate-the-destination-volume), ce qui permet au client de lire et d’écrire sur ce volume. 

Après la reprise d’activité, vous pouvez effectuer une restauration automatique vers le volume source à l’aide d’une [opération de resynchronisation](#resync-replication-to-reactivate-the-source-volume) qui remplace les données du volume source par celles du volume de destination.  Ensuite, vous [rétablissez la réplication de la source vers la destination](#reestablish-source-to-destination-replication) et remontez le volume source pour que le client puisse y accéder. 

Les détails sont décrits ci-dessous. 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>Arrêter le peering de réplication pour activer le volume de destination

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

## <a name="resync-replication-to-reactivate-the-source-volume"></a>Resynchroniser la réplication pour réactiver le volume source   

Après la reprise d’activité, vous pouvez réactiver le volume source en effectuant une opération de resynchronisation.  L’opération de resynchronisation inverse le processus de réplication et synchronise les données du volume de destination vers le volume source.  

> [!IMPORTANT] 
> L’opération de resynchronisation remplace les données du volume source par celles du volume de destination.  L’interface utilisateur vous avertit du risque de perte de données. Vous êtes invité à confirmer l’action de resynchronisation avant le démarrage de l’opération.

1. Pour resynchroniser la réplication, sélectionnez le volume *source*. Cliquez sur **Réplication** sous Service de stockage. Cliquez ensuite sur **Resynchroniser**.  

2. Tapez **Oui** quand vous y êtes invité, puis cliquez sur le bouton **Resynchroniser**. 
 
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
* [Métriques de réplication de volume](azure-netapp-files-metrics.md#replication)
* [Résoudre les problèmes de réplication inter-région](troubleshoot-cross-region-replication.md)

