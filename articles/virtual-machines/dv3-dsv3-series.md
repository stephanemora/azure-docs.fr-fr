---
title: Séries Dv3 et Dsv3
description: Spécifications pour les machines virtuelles des séries Dv3 et Dsv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: e4d5f2527c0ab8ff4026b93fd2250e324244634e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829235"
---
# <a name="dv3-and-dsv3-series"></a>Séries Dv3 et Dsv3

Les machines virtuelles de la série Dv3 s’exécutent sur les processeurs Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) dans une configuration hyper-thread, ce qui offre une meilleure valeur pour la plupart des charges de travail universelles. La mémoire a été étendue (d’environ 3,5 Gio/vCPU à 4 Gio/vCPU) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading. La série Dv3 n’offre plus les tailles de machine virtuelle à mémoire élevée de la série D/Dv2, qui ont été déplacées vers les séries [Ev3 et Esv3](ev3-esv3-series.md) à mémoire optimisée.

Voici des exemples de cas d’usage de la série D : applications de classe Entreprise, bases de données relationnelles, mise en cache en mémoire et analytique.

## <a name="dv3-series"></a>Série Dv3

Les tailles de la série Dv3 s’exécutent sur les processeurs Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. Les tailles des machines virtuelles de la série Dv3 offrent une combinaison de processeur virtuel, mémoire et stockage temporaire pour la plupart des charges de travail de production.

Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format Dsv3. Les tarifs et compteurs de facturation pour les tailles Dsv3 sont identiques à celles de la série Dv3.

Machines virtuelles de série Dv3 dotées de la technologie Hyper-Threading d’Intel®.

ACU : 160-190

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS/Mbits/s en lecture/Mbits/s en écriture | Nombre max de cartes réseau|Bande passante réseau |
|---|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2|1 000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8|24 000 |
| Standard_D64_v3 | 64 | 256 | 1 600 | 32 | 96000/1000/500 | 8|30000 |

## <a name="dsv3-series"></a>Dsv3-series

Les tailles de la série Dsv3 s’exécutent sur les processeurs Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0, et elles utilisent le Stockage Premium. Les tailles des machines virtuelles de la série Dsv3 offrent une combinaison de processeur virtuel, mémoire et stockage temporaire pour la plupart des charges de travail de production.

Machines virtuelles de série Dsv3 dotées de la technologie Hyper-Threading d’Intel®.

ACU : 160-190

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max de cartes réseau|Bande passante réseau attendue (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4 000/32 (50)       | 3 200/48    | 2|1 000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8 000/64 (100)      | 6 400/96    | 2|2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16 000/128 (200)    | 12 800/192  | 4|4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32 000/256 (400)    | 25 600/384  | 8|8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64 000/512 (800)    | 51 200/768  | 8|16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96 000/768 (1 200)   | 76 800/1152 | 8|24 000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128 000/1 024 (1 600) | 80 000/1 200 | 8|30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Pour plus d’informations sur les types de disques : [Types de disques](./linux/disks-types.md#ultra-disk)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.