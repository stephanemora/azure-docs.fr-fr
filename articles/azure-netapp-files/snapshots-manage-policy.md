---
title: Gérer des stratégies d’instantanés dans Azure NetApp Files | Microsoft Docs
description: Décrit comment créer, gérer, modifier et supprimer des stratégies d’instantanés à l’aide d’Azure NetApp Files.
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
ms.openlocfilehash: e4ccbd68ead83f682f39359c053e5a608d34903c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700533"
---
# <a name="manage-snapshot-policies-in-azure-netapp-files"></a>Gérer des stratégies d’instantanés dans Azure NetApp Files

Les [instantanés](snapshots-introduction.md) autorisent la récupération jusqu’à une date et heure des volumes. Vous pouvez planifier la prise automatique d’[instantanés de volume](snapshots-introduction.md) à l’aide de stratégies d’instantanés. Vous pouvez également modifier une stratégie de capture instantanée si nécessaire, ou supprimer en une dont vous n’avez plus besoin.  

## <a name="create-a-snapshot-policy"></a>Créer une stratégie de capture instantanée 

Une stratégie de capture instantanée vous permet de spécifier la fréquence de création de captures instantanées en cycles horaires, quotidiens, hebdomadaires ou mensuels. Vous devez également spécifier le nombre maximal de captures instantanées à conserver pour le volume.  

1.  Dans la vue Compte NetApp, cliquez sur **Stratégie de capture instantanée**.

    ![Capture d’écran montrant comment accéder à Stratégie d’instantané.](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Dans la fenêtre Stratégie de capture instantanée, définissez l’État de la stratégie sur **Activée**. 

3.  Cliquez sur l’un des onglets **Horaire**, **Quotidienne**, **Hebdomadaire** ou **Mensuelle** pour créer respectivement une stratégie de capture instantanée horaire, quotidienne, hebdomadaire ou mensuelle. Spécifiez le **Nombre de captures instantanées à conserver**.  

    Concernant le nombre maximal de captures instantanées autorisées pour un volume, consultez [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md). 

    L’exemple suivant présente la configuration de la stratégie de capture instantanée horaire. 

    ![Capture d’écran montrant la stratégie d’instantané horaire.](../media/azure-netapp-files/snapshot-policy-hourly.png)

    L’exemple suivant présente la configuration de la stratégie de capture instantanée quotidienne.

    ![Capture d’écran montrant la stratégie d’instantané quotidien.](../media/azure-netapp-files/snapshot-policy-daily.png)

    L’exemple suivant présente la configuration de la stratégie de capture instantanée hebdomadaire.

    ![Capture d’écran montrant la stratégie d’instantané hebdomadaire.](../media/azure-netapp-files/snapshot-policy-weekly.png)

    L’exemple suivant présente la configuration de la stratégie de capture instantanée mensuelle.

    ![Capture d’écran montrant la stratégie d’instantané mensuel.](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Cliquez sur **Enregistrer**.  

Si vous avez besoin de créer des stratégies de capture instantanée supplémentaires, répétez l’étape 3.
Les stratégies que vous avez créées apparaissent dans la page Stratégie de capture instantanée.

Si vous souhaitez qu’un volume utilise la stratégie de capture instantanée, vous devez [appliquer la stratégie au volume](snapshots-manage-policy.md#apply-a-snapshot-policy-to-a-volume). 

## <a name="apply-a-snapshot-policy-to-a-volume"></a>Appliquer une stratégie de capture instantanée à un volume

Si vous souhaitez qu’un volume utilise une stratégie de capture instantanée que vous avez créée, vous devez appliquer la stratégie au volume. 

Vous ne pouvez pas appliquer une stratégie d’instantané à un volume de destination dans une réplication entre régions.  

1.  Accédez à la page **Volumes**, cliquez avec le bouton droit sur le volume auquel vous souhaitez appliquer une stratégie de capture instantanée, puis sélectionnez **Modifier**.

    ![Capture d’écran montrant le menu contextuel de Volumes.](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Dans la fenêtre Modifier, sous **Stratégie de capture instantanée**, sélectionnez une stratégie à utiliser pour le volume.  Cliquez sur **OK** pour appliquer la stratégie.  

    ![Capture d’écran montrant le menu Stratégie d’instantané.](../media/azure-netapp-files/snapshot-policy-edit.png) 

## <a name="modify-a-snapshot-policy"></a>Modifier une stratégie de capture instantanée 

Vous pouvez modifier une stratégie de capture instantanée pour modifier l’état de la stratégie, la fréquence des captures instantanées (horaire, quotidienne, hebdomadaire ou mensuelle) ou le nombre de captures instantanées à conserver.  
 
1.  Dans la vue Compte NetApp, cliquez sur **Stratégie de capture instantanée**.

2.  Cliquez avec le bouton droit sur la stratégie de capture instantanée que vous souhaitez modifier, puis sélectionnez **Modifier**.

    ![Capture d’écran montrant le menu contextuel de Stratégie d’instantané.](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Apportez les modifications dans la fenêtre Stratégie de capture instantanée qui s’affiche, puis cliquez sur **Enregistrer**. 

## <a name="delete-a-snapshot-policy"></a>Supprimer une stratégie de capture instantanée 

Vous pouvez supprimer une stratégie de capture instantanée que vous ne souhaitez plus conserver.   

1.  Dans la vue Compte NetApp, cliquez sur **Stratégie de capture instantanée**.

2.  Cliquez avec le bouton droit sur la stratégie de capture instantanée que vous souhaitez modifier, puis sélectionnez **Supprimer**.

    ![Capture d’écran montrant l’élément de menu Supprimer.](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer la stratégie de capture instantanée.   

    ![Capture d’écran montrant la confirmation de suppression de la stratégie d’instantané.](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes de stratégies d’instantanés](troubleshoot-snapshot-policies.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [En savoir plus sur les instantanés](snapshots-introduction.md)