---
title: Créer un instantané à la demande à l’aide de Azure NetApp Files | Microsoft Docs
description: Décrit comment créer des captures instantanées à la demande à l’aide d’Azure NetApp Files.
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
ms.date: 08/12/2021
ms.author: b-juche
ms.openlocfilehash: 2a17ed67830dfcfc3f1c5c3cbd6cc06ceedc8028
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614179"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>Créer un instantané à la demande pour un volume

Azure NetApp Files prend en charge la création de [captures instantanées](snapshots-introduction.md) à la demande et l’utilisation de stratégies de capture instantanée pour planifier la création automatique de captures instantanées. Vous pouvez également restaurer un [instantané sur un nouveau volume](snapshots-restore-new-volume.md), [restaurer un fichier unique à l’aide d’un client](snapshots-restore-file-client.md), ou [rétablir un volume existant à l’aide d’un instantané](snapshots-revert-volume.md). Cet article explique comment créer un instantané à la demande pour un volume. 

> [!NOTE] 
> Pour plus d’informations sur la gestion des instantanés dans la réplication entre les régions, consultez [Configuration requise et considérations pour la réplication entre régions](cross-region-replication-requirements-considerations.md).
 
## <a name="steps"></a>Étapes

1.  Accédez au volume pour lequel vous souhaitez créer une capture instantanée. Cliquez sur **Captures instantanées**.

    ![Capture d’écran montrant comment accéder au panneau des captures instantanées.](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Cliquez sur **+ Ajouter une capture instantanée** afin de créer une capture instantanée à la demande pour un volume.

    ![Capture d’écran montrant comment ajouter une capture instantanée.](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Dans la fenêtre Nouvel instantané, spécifiez un nom pour l’instantané que vous créez.   

    ![Capture d’écran montrant la Nouvelle fenêtre de capture instantanée.](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Cliquez sur **OK**. 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les captures instantanées](snapshots-introduction.md)
* [Gérer les stratégies de captures instantanées avec Azure NetApp Files](snapshots-manage-policy.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vidéo sur les instantanés Azure NetApp Files](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Qu’est-ce que l’outil Azure Application Consistent Snapshot Tool ?](azacsnap-introduction.md)
