---
title: Supprimer des instantanés avec Azure NetApp Files | Microsoft Docs
description: Décrit comment supprimer des instantanés à l’aide d’Azure NetApp Files.
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
ms.openlocfilehash: 19ae00a47dfee9fc86a3c5c42b81821934ad5999
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700537"
---
# <a name="delete-snapshots-using-azure-netapp-files"></a>Supprimer des instantanés avec Azure NetApp Files 

Vous pouvez supprimer les instantanés dont vous n'avez plus besoin. 

> [!IMPORTANT]
> L’opération de suppression d’un instantané est irréversible. Impossible de récupérer un instantané supprimé. 

## <a name="steps"></a>Étapes

1. Accédez au menu **Instantanés** d’un volume. Cliquez avec le bouton droit sur l’instantané que vous voulez supprimer. Sélectionnez **Supprimer**.

    ![Capture d’écran qui décrit le menu contextuel d’un instantané](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Dans la fenêtre Supprimer l’instantané, confirmez que vous souhaitez supprimer l’instantané en cliquant sur **Oui**. 

    ![Capture d’écran confirmant la suppression de l’instantané](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les instantanés](snapshots-introduction.md)
* [Vue d’ensemble des instantanés Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)