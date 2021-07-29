---
title: Modification du niveau de performance des disques managés Azure avec le Portail Azure
description: Découvrez comment modifier le niveau de performance de nouveaux disques managés et de disques existants avec le Portail Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/13/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a82865930a5ed770b742c3d5e8906d4e69be3f64
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110079405"
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

### <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>Modifier le niveau de performance d’un disque sans temps d’arrêt (préversion)

Vous pouvez également modifier votre niveau de performance sans temps d’arrêt. Ainsi, vous n’avez pas besoin de libérer votre machine virtuelle ou de détacher votre disque pour modifier le niveau.

### <a name="prerequisites"></a>Prérequis

Votre disque doit répondre aux exigences énoncées dans la section [Modifier le niveau de performance sans temps d'arrêt (préversion)](#change-performance-tier-without-downtime-preview). Si ce n'est pas le cas, la modification du niveau de performance entraînera un temps d'arrêt.

Vous devez activer la fonctionnalité pour votre abonnement avant de pouvoir modifier le niveau de performance d'un disque sans temps d'arrêt. Suivez les étapes ci-dessous pour activer la fonctionnalité pour votre abonnement :

1.  Exécutez la commande suivante pour inscrire la fonctionnalité pour votre abonnement

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  Vérifiez que l'état de l'inscription est **Inscrit** (cela peut prendre quelques minutes) à l'aide de la commande suivante avant d'essayer la fonctionnalité.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="change-performance-tier"></a>Changer le niveau de performance

Maintenant que la fonctionnalité a été inscrite, vous pouvez modifier les niveaux de performances du disque concerné sans temps d’arrêt.

1. Connectez-vous au portail Azure à partir du lien suivant : [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview).
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
