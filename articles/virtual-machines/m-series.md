---
title: Série M – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série M.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 4050a74dfef1fb31939ba716cfbaf78577f1aaef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675942"
---
# <a name="m-series"></a>Série M

La série M propose un nombre élevé de processeurs virtuels (jusqu’à 128 processeurs virtuels) et une grande quantité de mémoire (jusqu’à 3,8 Tio). Elle est également idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire. Les tailles de la série M sont prises en charge à la fois sur le processeur Intel&reg; Xeon&reg; E7-8890 v3 à 2,50 GHz et sur le processeur Intel&reg; Xeon&reg; Platinum 8280M (Cascade Lake).

Les machines virtuelles de la série M sont dotées de la technologie Hyper-Threading d’Intel&reg;.

ACU : 160-180

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Migration dynamique : Non pris en charge

Mises à jour avec préservation de la mémoire : Non pris en charge

Accélérateur d’écriture :  [Pris en charge](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218.75 | 256   | 8  | 10 000/100 (793)     | 5 000/125   | 4/2 000  |
| Standard_M16ms                   | 16  | 437.5  | 512   | 16 | 20 000/200 (1587)    | 10 000/250  | 8/4 000  |
| Standard_M32ts                   | 32  | 192    | 1 024  | 32 | 40 000/400 (3174)    | 20 000/500  | 8/8 000  |
| Standard_M32ls                   | 32  | 256    | 1 024  | 32 | 40 000/400 (3174)    | 20 000/500  | 8/8 000  |
| Standard_M32ms                   | 32  | 875    | 1 024  | 32 | 40 000/400 (3174)    | 20 000/500  | 8/8 000  |
| Standard_M64s <sup>1</sup>       | 64  | 1 024   | 2 048  | 64 | 80 000/800 (6348)    | 40 000/1 000 | 8/16 000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2 048  | 64 | 80 000/800 (6348)    | 40 000/1 000 | 8/16 000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1792   | 2 048  | 64 | 80 000/800 (6348)    | 40 000/1 000 | 8/16 000 |
| Standard_M128s <sup>1</sup>    | 128 | 2 048   | 4096  | 64 | 160 000/1 600 (12696) | 80 000/2 000 | 8/30 000 |
| Standard_M128ms <sup>1,2</sup>   | 128 | 3892   | 4096  | 64 | 160 000/1 600 (12696) | 80 000/2 000 | 8/30 000 |
| Standard_M64 <sup>1</sup>        | 64  | 1 024   | 7168  | 64 | 80 000/800 (1228)    | 40 000/1 000 | 8/16 000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80 000/800 (1228)    | 40 000/1 000 | 8/16 000 |
| Standard_M128 <sup>1</sup>     | 128 | 2 048   | 14336 | 64 | 250 000/1 600 (2456)  | 80 000/2 000 | 8/32 000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250 000/1 600 (2456)  | 80 000/2 000 | 8/32 000 |

<sup>1</sup> Plus de 64 processeurs virtuels nécessitent l’un de ces systèmes d’exploitation invités pris en charge : Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 et Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 avec LIS 4.2.1.

<sup>2</sup> L’instance est isolée sur un matériel dédié à un client unique.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
