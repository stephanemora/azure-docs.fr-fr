---
title: Migrer des machines virtuelles Azure vers la fonctionnalité Disques managés | Microsoft Docs
description: Migrez des machines virtuelles créées à l’aide de disques non managés dans des comptes de stockage afin d’utiliser la fonctionnalité Disques managés.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: f1f786ea4fbf9cea5afbbd2ff038b2b3f8bc3eaf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56803581"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrer des machines virtuelles Azure vers la fonctionnalité Disques managés dans Azure

La fonctionnalité Disques managés simplifie la gestion de votre stockage en éliminant la nécessité de gérer séparément les comptes de stockage.  Vous pouvez également migrer vos machines virtuelles Azure existantes vers la fonctionnalité Disques managés afin de tirer parti de la fiabilité accrue des machines virtuelles dans un groupe à haute disponibilité. Cela permet de s’assurer que les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés les uns des autres pour éviter les points de défaillance uniques. Les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont automatiquement placés dans des unités d’échelle (tampons) de stockage distinctes, ce qui limite l’impact des défaillances d’unités d’échelle de stockage uniques dues à des défaillances matérielles et logicielles.
Selon vos besoins, vous avez le choix entre quatre types d’options de stockage. Pour en savoir plus sur les types de disques disponibles, consultez l'article [Sélectionner un type de disque](disks-types.md)

## <a name="migrate-scenarios"></a>Scénarios de migration

Vous pouvez migrer vers la fonctionnalité Disques managés dans les cas de figure suivants :

| **Migrer...**                                            | **Lien vers la documentation**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Convertir des machines virtuelles autonomes et des machines virtuelles dans un groupe à haute disponibilité en disques managés   | [Convertir des machines virtuelles pour utiliser des disques gérés](convert-unmanaged-to-managed-disks.md) |
| Une machine virtuelle unique du modèle Classic vers le modèle Resource Manager sur des disques managés     | [Créer une machine virtuelle à partir d’un disque dur virtuel classique](create-vm-specialized-portal.md)  | 
| Toutes les machines virtuelles d’un réseau virtuel du modèle Classic vers le modèle Resource Manager sur des disques managés     | [Migration de ressources IaaS d’un environnement Classic vers Resource Manager](migration-classic-resource-manager-ps.md), puis [Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la fonctionnalité [Disques managésdisques managés](managed-disks-overview.md)
- Consultez la [tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).
