---
title: Séries Dv2 et Dsv2 – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles des séries Dv2 et DSv2.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cdfb8e333e3834adf242029e9e00eac9cf9abebe
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492410"
---
# <a name="dv2-and-dsv2-series"></a>Séries Dv2 et DSv2

Les séries Dv2 et Dsv2, qui ont suivi la série D d’origine, sont dotées d’un processeur plus performant et d’une configuration avec un ratio processeur/mémoire optimal, ce qui en fait les séries idéales pour la plupart des charges de travail de production. La série Dv2 est environ 35 % plus rapide que la série D. La série Dv2 s’exécute sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

## <a name="dv2-series"></a>Série Dv2

Les tailles de la série Dv2 s’exécutent sur des processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0.

ACU : 210-250

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS/Mbits/s en lecture/Mbits/s en écriture | Disques de données max. | Débit : E/S par seconde | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4 x 500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2/1 500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4/3 000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8/6 000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64 x 500 | 8/12 000 |

## <a name="dsv2-series"></a>Séries DSv2

Les tailles de la série DSv2 s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0, et elles utilisent le Stockage Premium.

ACU : 210-250

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4 000/32 (43)    | 3 200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8 000/64 (86)    | 6 400/96   | 2/1 500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16 000/128 (172) | 12 800/192 | 4/3 000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32 000/256 (344) | 25 600/384 | 8/6 000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64 000/512 (688) | 51 200/768 | 8/12 000 |

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