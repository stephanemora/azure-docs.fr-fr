---
title: Modification du niveau de performance des disques managés Azure avec le Portail Azure
description: Découvrez comment modifier le niveau de performance de nouveaux disques managés et de disques existants avec le Portail Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901019"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Modification du niveau de performance avec le Portail Azure

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Prise en main

### <a name="new-disks"></a>Nouveaux disques

La procédure suivante montre comment modifier le niveau de performance d’un disque lors de sa création initiale :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à la machine virtuelle pour laquelle vous souhaitez créer un disque.
1. Lorsque vous sélectionnez le nouveau disque, commencez par choisir la taille dont vous avez besoin.
1. Une fois que vous avez sélectionné une taille, sélectionnez un autre niveau de performance pour modifier ses performances.
1. Sélectionnez **OK** pour créer le disque.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Capture d’écran du panneau de création de disque, avec un disque et la liste déroulante du niveau de performance surlignés" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Disques existants

La procédure suivante montre comment modifier le niveau de performance d’un disque existant :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à la machine virtuelle contenant le disque que vous souhaitez modifier.
1. Libérez la machine virtuelle ou détachez le disque.
1. Sélectionnez votre disque.
1. Sélectionnez **Taille + performances**.
1. Dans la liste déroulante **Niveau de performance**, sélectionnez un niveau différent du niveau de performance actuel du disque.
1. Sélectionnez **Redimensionner**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Capture d’écran du panneau Taille + performances, avec le niveau de performance surligné" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de redimensionner un disque pour tirer parti de niveaux de performance supérieurs, consultez les articles suivants :

- [Étendre des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI](linux/expand-disks.md)
- [Développer un disque managé attaché à une machine virtuelle Windows](windows/expand-os-disk.md)
