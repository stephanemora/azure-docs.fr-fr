---
title: Série de mémoire moyenne Msv2/Mdsv2 - Machines virtuelles Azure
description: Spécifications pour les machines virtuelles de la série Msv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 696125f75e41d39b4f9aaa1ff852b03eff589fb0
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108204240"
---
# <a name="msv2-and-mdsv2-series-medium-memory"></a>Mémoire moyenne des séries Msv2 et Mdsv2

Les machines virtuelles de mémoire moyenne des séries Msv2 et Mdsv2 sont dotées d’un processeur Intel® Xeon® Platinum 8280 (Cascade Lake) avec une fréquence de base pour tous les cœurs de 2,7 GHz et une fréquence turbo monocœur de 4,0 GHz. Avec ces machines virtuelles, les clients bénéficient d’une flexibilité accrue grâce aux options de disque local et sans disque. Les clients ont également accès à un ensemble de nouvelles tailles de machines virtuelles isolées avec des capacités accrues de processeur et de mémoire allant jusqu’à 192 processeurs virtuels avec 4 Tio de mémoire. 

> [!NOTE]
> Les machines virtuelles à mémoire moyenne Msv2 et Mdsv2 appartiennent à la 2e génération. Pour plus d'informations sur les machines virtuelles de 2e génération, consultez [Prise en charge des machines virtuelles de 2e génération dans Azure](./generation-2.md).



[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 2<br>
[Accélérateur d’écriture](./how-to-enable-write-accelerator.md) : Pris(e) en charge<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 mémoire moyenne sans disque 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max de cartes réseau | Bande passante réseau attendue (Mbit/s) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20 000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1 024 | 0 | 64 | 40 000/1 000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40 000/1 000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2 048 | 0 | 64 | 80 000/2 000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80 000/2 000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2 048 | 0 | 64 | 80 000/2 000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80 000/2 000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 mémoire moyenne avec disque  

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Nombre max. de disques de données | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max de cartes réseau | Bande passante réseau attendue (Mbit/s) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1 024 | 32 | 40 000/400 | 20 000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1 024 | 2 048 | 64 | 80 000/800 | 40 000/1 000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2 048 | 64 | 80 000/800 | 40 000/1 000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2 048 | 4096 | 64 |160 000/1 600 | 80 000/2 000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160 000/1 600 | 80 000/2 000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2 048 | 4096 | 64 | 160 000/1 600 | 80 000/2 000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160 000/1 600 | 80 000/2 000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
