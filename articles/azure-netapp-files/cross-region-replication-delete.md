---
title: Supprimer des réplications pour la réplication d’Azure NetApp Files entre régions | Microsoft Docs
description: Décrit la procédure de suppression d’une connexion de réplication qui n’est plus nécessaire entre les volumes source et de destination.
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
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695705"
---
# <a name="delete-replications"></a>Supprimer des réplications

Pour mettre fin à la connexion de réplication entre les volumes source et de destination, supprimez la réplication du volume. Vous pouvez effectuer l’opération de suppression à partir du volume source ou du volume de destination. L’opération de suppression ne supprime que l’autorisation pour la réplication ; elle ne supprime pas le volume source ni le volume de destination. 

## <a name="steps"></a>Étapes

1. Assurez-vous que le peering de réplication a été interrompu avant de supprimer la réplication de volume.    
    Voir [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md) et [Arrêter le peering de réplication](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume).  

1. Pour supprimer la réplication du volume, sélectionnez **Réplication** sur le volume source ou le volume de destination.  

2. Cliquez sur **Supprimer**.    

3. Pour confirmer la suppression, tapez **Oui** et cliquez sur **Supprimer**.   

    ![Supprimer la réplication](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Réplication entre régions](cross-region-replication-introduction.md)
* [Configuration requise et considérations pour la réplication entre régions](cross-region-replication-requirements-considerations.md)
* [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md)
* [Résoudre les problèmes de réplication entre régions](troubleshoot-cross-region-replication.md)

