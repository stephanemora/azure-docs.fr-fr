---
title: Rétablir un instantané de volume avec Azure NetApp Files | Microsoft Docs
description: Décrit comment revenir à l’état antérieur d’un volume à l’aide d’Azure NetApp Files.
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
ms.openlocfilehash: 7bf50ee981052424cf0a57f366b3e24b483eb5ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700664"
---
# <a name="revert-a-volume-using-snapshot-revert-with-azure-netapp-files"></a>Rétablir un instantané de volume avec Azure NetApp Files

La fonctionnalité de rétablissement d’[instantané](snapshots-introduction.md) vous permet de rétablir rapidement un volume à l’état dans lequel il se trouvait lors de la prise d’un instantané particulier. Dans la plupart des cas, le rétablissement d’un volume est beaucoup plus rapide que la restauration de fichiers individuels, d’un instantané vers le système de fichiers actif. La gestion de l’espace est également mieux optimisée par rapport à la restauration d’un instantané sur un nouveau volume. 

Vous trouverez l’option Rétablir le volume dans le menu Instantanés d’un volume. Après avoir choisi un instantané à restaurer, Azure NetApp Files restaure le volume avec les données et horodatages qu’il contenait lors de la capture de l’instantané sélectionné. 

> [!IMPORTANT]
> Les données du système de fichiers et instantanés actifs créés après l’instantané sélectionné seront perdus. L’opération de restauration de l’instantané remplacera *toutes* les données du volume ciblé par les données de l’instantané sélectionné. Vous devez prêter attention au contenu de l’instantané et à la date de création lorsque vous sélectionnez un instantané. Vous ne pouvez pas annuler l’opération de restauration de l’instantané.

## <a name="steps"></a>Étapes

1. Accédez au menu **Instantanés** d’un volume.  Cliquez avec le bouton droit sur l’instantané que vous souhaitez utiliser pour l’opération de restauration. Sélectionnez **Rétablir le volume**. 

    ![Capture d’écran qui décrit le menu contextuel d’un instantané.](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Dans la fenêtre Rétablir l'instantané du volume, entrez le nom du volume, puis cliquez sur **Restaurer**.   

    Le volume est maintenant restauré au point chronologique de l’instantané sélectionné.

![Capture d’écran de la fenêtre Rétablir l’instantané du volume.](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les instantanés](snapshots-introduction.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vidéo sur les instantanés Azure NetApp Files](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Vue d’ensemble des instantanés Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)