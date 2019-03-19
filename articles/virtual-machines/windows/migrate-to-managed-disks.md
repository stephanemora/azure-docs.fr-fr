---
title: Migrer des machines virtuelles Azure vers des disques gérés | Microsoft Docs
description: Migrez des machines virtuelles créées à l’aide de disques non gérés dans des comptes de stockage afin d’utiliser des disques gérés.
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
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56803581"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrer des machines virtuelles Azure vers des disques gérés dans Azure

Les disques gérés du service Azure Managed Disks simplifient la gestion de votre stockage en éliminant la nécessité de gérer séparément les comptes de stockage.  Vous pouvez également migrer vos machines virtuelles Azure existantes vers des disques gérés afin de tirer parti de la fiabilité accrue des machines virtuelles dans un groupe à haute disponibilité. Cela permet de s’assurer que les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés les uns des autres pour éviter les points de défaillance uniques. Les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont automatiquement placés dans des unités d’échelle (tampons) de stockage distinctes, ce qui limite l’impact des défaillances d’unités d’échelle de stockage uniques dues à des défaillances matérielles et logicielles.
Selon vos besoins, vous avez le choix entre quatre types d’options de stockage. Pour en savoir plus sur les types de disques disponibles, consultez l'article [Sélectionner un type de disque](disks-types.md)

## <a name="migrate-scenarios"></a>Scénarios de migration

Vous pouvez migrer vers des disques gérés dans les cas de figure suivants :

| **Migrer...**                                            | **Lien vers la documentation**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Convertir des machines virtuelles autonomes et des machines virtuelles dans un groupe à haute disponibilité en disques gérés   | [Convertir des machines virtuelles pour utiliser des disques gérés](convert-unmanaged-to-managed-disks.md) |
| Une machine virtuelle unique du modèle Classic vers le modèle Resource Manager sur des disques gérés     | [Créer une machine virtuelle à partir d’un disque dur virtuel classique](create-vm-specialized-portal.md)  | 
| Toutes les machines virtuelles d’un réseau virtuel du modèle Classic vers le modèle Resource Manager sur des disques gérés     | [Migration de ressources IaaS d’un environnement Classic vers Resource Manager](migration-classic-resource-manager-ps.md), puis [Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [disques gérés](managed-disks-overview.md)
- Consultez la [tarification des disques gérés](https://azure.microsoft.com/pricing/details/managed-disks/).
