---
title: Migrer des machines virtuelles Azure vers la fonctionnalité Disques managés | Microsoft Docs
description: Migrez des machines virtuelles créées à l’aide de disques non managés dans des comptes de stockage afin d’utiliser la fonctionnalité Disques managés.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418402"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrer des machines virtuelles Azure vers la fonctionnalité Disques managés dans Azure

La fonctionnalité Disques managés simplifie la gestion de votre stockage en éliminant la nécessité de gérer séparément les comptes de stockage.  Vous pouvez également migrer vos machines virtuelles Azure existantes vers la fonctionnalité Disques managés afin de tirer parti de la fiabilité accrue des machines virtuelles dans un groupe à haute disponibilité. Elle garantit que les disques des différentes machines virtuelles dans un groupe à haute disponibilité sont suffisamment isolés les uns des autres pour éviter les points de défaillance uniques. Les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont automatiquement placés dans des unités d’échelle (tampons) de stockage distinctes, ce qui limite l’impact des défaillances d’unités d’échelle de stockage uniques dues à des défaillances matérielles et logicielles.
Selon vos besoins, vous avez le choix entre quatre types d’options de stockage. Pour en savoir plus sur les types de disques disponibles, consultez l'article [Sélectionner un type de disque](disks-types.md)

## <a name="migration-scenarios"></a>Scénarios de migration

Vous pouvez migrer vers la fonctionnalité Disques managés dans les cas de figure suivants :

|Scénario  |Article  |
|---------|---------|
|Convertir des machines virtuelles autonomes et des machines virtuelles dans un groupe à haute disponibilité en disques managés     |[Convertir des machines virtuelles pour utiliser des disques gérés](convert-unmanaged-to-managed-disks.md)         |
|Convertissez une seule machine virtuelle classique vers Resource Manager sur des disques gérés     |[Créer une machine virtuelle à partir d’un disque dur virtuel classique](create-vm-specialized-portal.md)         |
|Convertissez toutes les machines virtuelles dans un réseau virtuel classique vers Resource Manager sur des disques gérés     |[Migration de ressources IaaS d’un environnement Classic vers Resource Manager](migration-classic-resource-manager-ps.md), puis [Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés](convert-unmanaged-to-managed-disks.md)         |
|Mettre à niveau des machines virtuelles avec disques non gérés standard vers des machines virtuelles avec disques gérés premium     | Tout d’abord, [convertir une machine virtuelle de Windows à partir de disques non gérés vers des disques gérés](convert-unmanaged-to-managed-disks.md). Puis [mettre à jour le type de stockage d’un disque géré](convert-disk-storage.md).         |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la fonctionnalité [Disques managésdisques managés](managed-disks-overview.md)
- Consultez la [tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).
