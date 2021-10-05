---
title: Gérer les stratégies de sauvegarde pour Azure NetApp Files | Microsoft Docs
description: Décrit comment modifier ou suspendre une stratégie de sauvegarde pour des volumes de Azure NetApp Files.
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
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: c3f440899e53125b6a7e6ef7986d74e4f1d0977d
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095053"
---
# <a name="manage-backup-policies-for-azure-netapp-files"></a>Gérer les stratégies de sauvegarde pour Azure NetApp Files 

Une configuration de stratégie d’instantané est nécessaire pour Azure NetApp Files [sauvegardes basées](backup-configure-policy-based.md) sur des stratégies pour sauvegarder automatiquement un volume. Une fois que vous avez configuré les sauvegardes Azure NetApp Files à l’aide d’une stratégie de sauvegarde, vous pouvez modifier ou suspendre une stratégie de sauvegarde en fonction des besoins.  

Les sauvegardes manuelles ne sont pas affectées par les modifications apportées à la stratégie de sauvegarde.

## <a name="modify-a-backup-policy"></a>Modifier une stratégie de sauvegarde   

Vous pouvez modifier une stratégie de sauvegarde de Azure NetApp Files existante en fonction des besoins afin de garantir une couverture de sauvegarde correcte pour les volumes Azure NetApp Files.  Par exemple, si vous devez modifier le nombre de sauvegardes conservées qui sont protégées par le service, vous pouvez modifier la stratégie de sauvegarde Azure NetApp Files pour le volume afin de modifier le nombre de restaurations à conserver. 

Pour modifier les paramètres de stratégie de sauvegarde :   

1. Accéder aux **Sauvegardes**.  

2. Sélectionnez **Stratégies de sauvegarde**, cliquez sur les trois points (`…`) à droite d’une stratégie de sauvegarde, puis cliquez sur **Modifier**.

    ![Capture d’écran montrant le menu contextuel des stratégies de sauvegarde.](../media/azure-netapp-files/backup-policies-edit.png)

3. Dans la fenêtre Modifier la stratégie de sauvegarde, mettez à jour le nombre de restaurations que vous souhaitez conserver pour les sauvegardes quotidiennes, hebdomadaires et mensuelles. Entrez le nom de la stratégie de sauvegarde pour confirmer l’action. Cliquez sur **Enregistrer**.  

    ![Capture d’écran qui montre la fenêtre Modifier la stratégie de sauvegarde.](../media/azure-netapp-files/backup-modify-policy.png)

    > [!NOTE] 
    > Une fois que les sauvegardes sont activées et ont pris effet pour la fréquence planifiée, vous ne pouvez pas modifier le nombre de rétention de sauvegarde en `0` . Un nombre minimal de `1` rétentions est requis pour la stratégie de sauvegarde. Consultez [Limites de ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md) pour plus d’informations.  

## <a name="suspend-a-backup-policy"></a>Suspendre une stratégie de sauvegarde  

Une stratégie de sauvegarde peut être interrompue pour ne pas effectuer de nouvelles opérations de sauvegarde sur les volumes associés. Cette action vous permet d’interrompre temporairement les sauvegardes, dans le cas où les sauvegardes existantes doivent être conservées, mais pas supprimées en raison du contrôle de version.   

### <a name="suspend-a-backup-policy-for-all-volumes-associated-with-the-policy"></a>Suspendre une stratégie de sauvegarde pour tous les volumes associés à la stratégie

1. Accéder aux **Sauvegardes**.

2. Sélectionnez **Stratégies de sauvegarde**, cliquez sur les trois points (`…`) à droite d’une stratégie de sauvegarde, puis cliquez sur **Modifier**. 

3. Basculer **État de la stratégie** sur **Désactivé**, entrez le nom de la stratégie à confirmer, puis cliquez sur **Enregistrer**. 

    ![Capture d’écran montrant la fenêtre Modifier la stratégie de sauvegarde avec l’état de la stratégie désactivé.](../media/azure-netapp-files/backup-modify-policy-disabled.png)

### <a name="suspend-a-backup-policy-for-a-specific-volume"></a>Suspendre une stratégie de sauvegarde pour un volume spécifique 

1. Allez sur **Volumes**. 
2. Sélectionnez le volume spécifique dont vous souhaitez suspendre les sauvegardes.
3. Sélectionnez **Configurer**.
4. Dans la page Configurer les sauvegardes, basculez **État de la stratégie** sur **Interrompre**, entrez le nom du volume à confirmer, puis cliquez sur **OK**.   

    ![Capture d’écran montrant la fenêtre Configurer les sauvegardes avec l’État suspendre la stratégie.](../media/azure-netapp-files/backup-modify-policy-suspend.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Exigences et considérations relatives à la sauvegarde Azure NetApp Files](backup-requirements-considerations.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration de sauvegardes basées sur une stratégie](backup-configure-policy-based.md)
* [Configuration des sauvegardes manuelles](backup-configure-manual.md)
* [Recherche dans les sauvegardes](backup-search.md)
* [Restauration d’une sauvegarde sur un nouveau volume](backup-restore-new-volume.md)
* [Désactivation de la fonctionnalité de sauvegarde pour un volume](backup-disable.md)
* [Supprimer des sauvegardes d’un volume](backup-delete.md)
* [Métriques des sauvegardes de volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



