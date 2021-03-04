---
title: Gérer les ressources créées lors du déplacement d’une machine virtuelle dans Azure Resource Mover
description: Apprenez à gérer les ressources créées lors du déplacement d’une machine virtuelle dans Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: d3c4c4e86e2461ea1d05af284e724a5a2991f040
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727037"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Gérer les ressources créées pour le déplacement d’une machine virtuelle

Cet article explique comment gérer les ressources créées explicitement par [Azure Resource Mover](overview.md) pour faciliter le processus de déplacement d’une machine virtuelle. 

Après le déplacement de machines virtuelles entre régions, un certain nombre de ressources créées par le moteur de ressources doivent être nettoyées manuellement.

## <a name="delete-resources-created-for-vm-move"></a>Supprimer les ressources créées pour le déplacement de machines virtuelles

Supprimez manuellement la collection de déplacement et les ressources Site Recovery créées pour le déplacement des machines virtuelles.

1. Examinez les ressources dans le groupe de ressources ```ResourceMoverRG-<sourceregion>-<target-region>-<metadataRegionShortName>```.
2. Vérifiez que la machine virtuelle et toutes les autres ressources sources de la collection de déplacement ont été déplacées ou supprimées. Cette vérification permet de s’assurer qu’aucune ressource en attente ne les utilise.
2. Supprimez ces ressources.

    - Le nom de la collection de déplacement est ```movecollection-<sourceregion>-<target-region>-<metadata-region>```.
    - Le nom du compte de stockage de cache est ```resmovecache<guid>```.
    - Le nom du coffre est ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Étapes suivantes

Essayez de [déplacer une machine virtuelle](tutorial-move-region-virtual-machines.md) vers une autre région avec Resource Mover.
