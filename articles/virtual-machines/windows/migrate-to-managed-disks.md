---
title: Migrer des machines virtuelles Azure vers la fonctionnalité Disques managés
description: Migrez des machines virtuelles créées à l’aide de disques non managés dans des comptes de stockage afin d’utiliser la fonctionnalité Disques managés.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 65b36db5185390cd6527a66c91eecd4712623df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564337"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrer des machines virtuelles Azure vers la fonctionnalité Disques managés dans Azure

La fonctionnalité Disques managés simplifie la gestion de votre stockage en éliminant la nécessité de gérer séparément les comptes de stockage.  Vous pouvez également migrer vos machines virtuelles Azure existantes vers la fonctionnalité Disques managés afin de tirer parti de la fiabilité accrue des machines virtuelles dans un groupe à haute disponibilité. Cela permet de s’assurer que les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés les uns des autres pour éviter un point de défaillance unique. Les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont automatiquement placés dans des unités d’échelle (tampons) de stockage distinctes, ce qui limite l’impact des défaillances d’unités d’échelle de stockage uniques dues à des défaillances matérielles et logicielles.
Selon vos besoins, vous avez le choix entre quatre types d’options de stockage. Pour en savoir plus sur les types de disques disponibles, consultez l'article [Sélectionner un type de disque](../disks-types.md)

## <a name="migration-scenarios"></a>Scénarios de migration

Vous pouvez migrer vers la fonctionnalité Disques managés dans les cas de figure suivants :

|Scénario  |Article  |
|---------|---------|
|Convertir des machines virtuelles autonomes et des machines virtuelles dans un groupe à haute disponibilité en disques managés     |[Convertir des machines virtuelles pour utiliser des disques managés](convert-unmanaged-to-managed-disks.md)         |
|Convertir une machine virtuelle unique du modèle Classic vers le modèle Resource Manager sur des disques managés     |[Créer une machine virtuelle à partir d’un disque dur virtuel classique](create-vm-specialized-portal.md)         |
|Convertir toutes les machines virtuelles d’un réseau virtuel du modèle Classic vers le modèle Resource Manager sur des disques managés     |[Migration de ressources IaaS d’un environnement Classic vers Resource Manager](../migration-classic-resource-manager-ps.md), puis [Convertir une machine virtuelle à partir de disques non managés vers des disques managés](convert-unmanaged-to-managed-disks.md)         |
|Mettre à niveau des machines virtuelles dotées de disques non managés Standard vers des machines virtuelles dotées de disques managés Premium     | Tout d’abord, [convertissez les disques non managés d’une machine virtuelle Windows en disques managés](convert-unmanaged-to-managed-disks.md). Ensuite, [mettez à jour le type de stockage d’un disque managé](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la fonctionnalité [Disques managésdisques managés](../managed-disks-overview.md)
- Consultez la [tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).