---
title: Séries Ev3 et Esv3
description: Spécifications pour les machines virtuelles des séries Ev3 et Esv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 86de6f94dc5bf44836fad37680a439d26d66ce6d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837072"
---
# <a name="ev3-and-esv3-series"></a>Séries Ev3 et Esv3

Les séries Ev3 et Esv3 disposent du processeur Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) dans une configuration hyperthread, ce qui leur permet d’offrir ce qui se fait de mieux pour la plupart des charges de travail à usage général et d’aligner la série Ev3 sur les machines virtuelles à usage général de la plupart des autres clouds.  La mémoire a été étendue (de 7 Gio/vCPU à 8 Gio/vCPU) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading. La série Ev3 constitue la suite des tailles de machines virtuelles à mémoire haute des familles D/Dv2.

## <a name="ev3-series"></a>Série Ev3

Les instances de la série Ev3 sont basées sur des fonctionnalités des processeurs Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) avec la technologie Intel Turbo Boost 2.0. Les tailles des machines virtuelles de la série ESv3 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.

Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format ESv3. Les tarifs et compteurs de facturation pour les tailles ESv3 sont identiques à ceux de la série Ev3.

Machines virtuelles de série Ev3 dotées de la technologie Hyper-Threading d’Intel®.

ACU : 160 - 190

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Nombre max de cartes réseau | Bande passante réseau |
|---|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2|1 000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8|10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8|24 000 |
| Standard_E64_v3 | 64 | 432 | 1 600 | 32 | 96000/1000/500 | 8|30000 |
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1 600 | 32 | 96000/1000/500 | 8|30000 |

<sup>1</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>2</sup> L’instance est isolée sur un matériel dédié à un client unique.

## <a name="esv3-series"></a>Série Esv3

Les instances de la série Esv3 sont basées sur des fonctionnalités des processeurs Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) avec la technologie Intel Turbo Boost 2.0, et utilisent un stockage Premium. Les tailles des machines virtuelles de la série ESv3 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.

Machines virtuelles de série Esv3 dotées de la technologie Hyper-Threading d’Intel®.

ACU : 160-190

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max de cartes réseau|Bande passante réseau attendue (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4 000/32 (50) | 3 200/48 | 2/1 000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8 000/64 (100)   | 6 400/96   | 2|2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16 000/128 (200) | 12 800/192 | 4|4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32 000/256 (400) | 25 600/384 | 8|8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40 000/320 (400) | 32 000/480 | 8/10 000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64 000/512 (800)    | 51 200/768  | 8|16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96 000/768 (1 200)   | 76 800/1152 | 8|24 000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128 000/1 024 (1 600) | 80 000/1 200 | 8|30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128 000/1 024 (1 600) | 80 000/1 200 | 8|30000 |

<sup>1</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>2</sup> L’instance est isolée sur un matériel dédié à un client unique.

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