---
title: Gérer les instantanés avec Azure NetApp Files | Microsoft Docs
description: Décrit comment créer et gérer des captures instantanées à l’aide d’Azure NetApp Files.
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 85990aee5143c9ccc0362a00597a748763977204
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080213"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gérer les instantanés avec Azure NetApp Files

Azure NetApp Files prend en charge la création de captures instantanées à la demande et l’utilisation de stratégies de capture instantanée pour planifier la création automatique de captures instantanées.  Vous pouvez également restaurer une capture instantanée sur un nouveau volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Créer un instantané à la demande pour un volume

Vous pouvez créer des captures instantanées de volume à la demande. 

1.  Accédez au volume pour lequel vous souhaitez créer une capture instantanée. Cliquez sur **Captures instantanées**.

    ![Accéder à des captures instantanées](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Cliquez sur **+ Ajouter une capture instantanée** afin de créer une capture instantanée à la demande pour un volume.

    ![Ajouter une capture instantanée](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Dans la fenêtre Nouvel instantané, spécifiez un nom pour l’instantané que vous créez.   

    ![Nouvelle capture instantanée](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Cliquez sur **OK**. 

## <a name="manage-snapshot-policies"></a>Gérer les stratégies de capture instantanée

Vous pouvez planifier la prise automatique de captures instantanées de volume à l’aide de stratégies de capture instantanée. Vous pouvez également modifier une stratégie de capture instantanée si nécessaire, ou supprimer en une dont vous n’avez plus besoin.  

### <a name="register-the-feature"></a>Inscrire la fonctionnalité

La fonctionnalité **stratégie d’instantané** est actuellement en préversion. Si vous utilisez cette fonctionnalité pour la première fois, vous devez commencer par l’inscrire. 

1. Inscrivez la fonctionnalité : 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Vérifiez l’état d’inscription de la fonctionnalité : 

    > [!NOTE]
    > **RegistrationState** peut être à l’état `Registering` pendant plusieurs minutes, et jusqu’à 60 minutes, avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit **Inscrit**.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>Créer une stratégie de capture instantanée 

Une stratégie de capture instantanée vous permet de spécifier la fréquence de création de captures instantanées en cycles horaires, quotidiens, hebdomadaires ou mensuels. Vous devez également spécifier le nombre maximal de captures instantanées à conserver pour le volume.  

1.  Dans la vue Compte NetApp, cliquez sur **Stratégie de capture instantanée**.

    ![Navigation dans la stratégie de capture instantanée](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Dans la fenêtre Stratégie de capture instantanée, définissez l’État de la stratégie sur **Activée**. 

3.  Cliquez sur l’un des onglets **Horaire**, **Quotidienne**, **Hebdomadaire** ou **Mensuelle** pour créer respectivement une stratégie de capture instantanée horaire, quotidienne, hebdomadaire ou mensuelle. Spécifiez le **Nombre de captures instantanées à conserver**.  

    Concernant le nombre maximal de captures instantanées autorisées pour un volume, consultez [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md). 

    L’exemple suivant présente la configuration de la stratégie de capture instantanée horaire. 

    ![Stratégie de capture instantanée horaire](../media/azure-netapp-files/snapshot-policy-hourly.png)

    L’exemple suivant présente la configuration de la stratégie de capture instantanée quotidienne.

    ![Stratégie de capture instantanée quotidienne](../media/azure-netapp-files/snapshot-policy-daily.png)

    L’exemple suivant présente la configuration de la stratégie de capture instantanée hebdomadaire.

    ![Stratégie de capture instantanée hebdomadaire](../media/azure-netapp-files/snapshot-policy-weekly.png)

    L’exemple suivant présente la configuration de la stratégie de capture instantanée mensuelle.

    ![Stratégie de capture instantanée mensuelle](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Cliquez sur **Enregistrer**.  

Si vous avez besoin de créer des stratégies de capture instantanée supplémentaires, répétez l’étape 3.
Les stratégies que vous avez créées apparaissent dans la page Stratégie de capture instantanée.

Si vous souhaitez qu’un volume utilise la stratégie de capture instantanée, vous devez [appliquer la stratégie au volume](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Appliquer une stratégie de capture instantanée à un volume

Si vous souhaitez qu’un volume utilise une stratégie de capture instantanée que vous avez créée, vous devez appliquer la stratégie au volume. 

1.  Accédez à la page **Volumes**, cliquez avec le bouton droit sur le volume auquel vous souhaitez appliquer une stratégie de capture instantanée, puis sélectionnez **Modifier**.

    ![Menu contextuel Volumes](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Dans la fenêtre Modifier, sous **Stratégie de capture instantanée**, sélectionnez une stratégie à utiliser pour le volume.  Cliquez sur **OK** pour appliquer la stratégie.  

    ![Modification de stratégie de capture instantanée](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Modifier une stratégie de capture instantanée 

Vous pouvez modifier une stratégie de capture instantanée pour modifier l’état de la stratégie, la fréquence des captures instantanées (horaire, quotidienne, hebdomadaire ou mensuelle) ou le nombre de captures instantanées à conserver.  
 
1.  Dans la vue Compte NetApp, cliquez sur **Stratégie de capture instantanée**.

2.  Cliquez avec le bouton droit sur la stratégie de capture instantanée que vous souhaitez modifier, puis sélectionnez **Modifier**.

    ![Menu contextuel Stratégie de capture instantanée](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Apportez les modifications dans la fenêtre Stratégie de capture instantanée qui s’affiche, puis cliquez sur **Enregistrer**. 

### <a name="delete-a-snapshot-policy"></a>Supprimer une stratégie de capture instantanée 

Vous pouvez supprimer une stratégie de capture instantanée que vous ne souhaitez plus conserver.   

1.  Dans la vue Compte NetApp, cliquez sur **Stratégie de capture instantanée**.

2.  Cliquez avec le bouton droit sur la stratégie de capture instantanée que vous souhaitez modifier, puis sélectionnez **Supprimer**.

    ![Menu contextuel Stratégie de capture instantanée](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer la stratégie de capture instantanée.   

    ![Confirmation de suppression de stratégie de capture instantanée](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurer un instantané sur un nouveau volume

Actuellement, vous pouvez restaurer un instantané seulement sur un nouveau volume. 
1. Dans le panneau Volume, sélectionnez **Captures instantanées** pour afficher la liste des captures instantanées. 
2. Cliquez avec le bouton droit sur la capture instantanée, puis sélectionnez l’option de menu **Restaurer sur un nouveau volume**.  

    ![Restaurer un instantané sur un nouveau volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Dans la fenêtre Créer un volume, spécifiez les informations concernant le nouveau volume :  
    * **Nom**   
        Spécifiez le nom du volume que vous créez.  
        
        Le nom doit être unique au sein d’un groupe de ressources. Il doit comprendre au moins trois caractères.  Vous pouvez utiliser tous les caractères alphanumériques.

    * **Quota**  
        Spécifiez la quantité de stockage logique à allouer au volume.  

    ![Restaurer sur un nouveau volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Cliquez sur **Vérifier + créer**.  Cliquez sur **Créer**.   
    Le nouveau volume utilise le même protocole que celui qu’utilise la capture instantanée.   
    Le nouveau volume sur lequel l’instantané est restauré apparaît dans le panneau Volumes.

## <a name="next-steps"></a>Étapes suivantes

* [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
