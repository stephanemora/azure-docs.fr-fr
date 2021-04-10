---
title: Série Msv2 (préversion) – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série Msv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562569"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Mémoire moyenne des séries Msv2 et Mdsv2 (préversion)


> [!IMPORTANT]
> Accédez à la préversion en remplissant le formulaire à l’adresse **https://aka.ms/Mv2MedMemoryPreview** .  

Les machines virtuelles de mémoire moyenne des séries Msv2 et Mdsv2 sont dotées d’un processeur Intel® Xeon® Platinum 8280 (Cascade Lake) avec une fréquence de base pour tous les cœurs de 2,7 GHz et une fréquence turbo monocœur de 4,0 GHz. Avec ces machines virtuelles, les clients bénéficient d’une flexibilité accrue grâce aux options de disque local et sans disque. Les clients ont également accès à un ensemble de nouvelles tailles de machines virtuelles isolées avec des capacités accrues de processeur et de mémoire allant jusqu’à 192 processeurs virtuels avec 4 Tio de mémoire. 


Les machines virtuelles des séries Msv2 et Mdsv2 sont uniquement de 2e génération et prennent en charge un sous-ensemble d’images compatibles avec la 2e génération. Pour obtenir la liste complète des images prises en charge pour les séries Msv2 et Mdsv2, voir ci-dessous.  

- Windows Server 2019 ou version ultérieure
- SUSE Linux Enterprise Server 12 SP4 et versions ultérieures ou SUSE Linux Enterprise Server 15 SP1 et versions ultérieures
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 ou version ultérieure 
- Oracle Enterprise Linux 7.7 ou version ultérieure

Pour plus d’informations sur les machines virtuelles de 2e génération, consultez [Prise en charge des machines virtuelles de 2e génération dans Azure](./generation-2.md).



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
