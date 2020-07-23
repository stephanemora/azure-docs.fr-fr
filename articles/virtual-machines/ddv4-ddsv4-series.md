---
title: Séries Ddv4 et Ddsv4
description: Spécifications relatives aux machines virtuelles des séries Dv4, Ddv4, Dsv4 et Ddsv4.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: b5d23442af5afb90071dbf9e3c50d42b91d195d1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494834"
---
# <a name="ddv4-and-ddsv4-series"></a>Séries Ddv4 et Ddsv4

Les séries Ddv4 et Ddsv4 s'exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8272CL (Cascade Lake) dans une configuration de type « Hyper-Threading » qui apporte davantage de valeur ajoutée à la plupart des charges de travail à usage général. Caractéristiques : vitesse d'horloge Turbo continue de 3,4 GHz, [technologie Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [technologie Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Elles prennent également en charge la technologie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Ces nouvelles tailles de machines virtuelles disposeront d'un stockage local 50 % plus volumineux, ainsi que de meilleures IOPS de disque local en lecture et en écriture par rapport aux tailles [Dv3/Dsv3](./dv3-dsv3-series.md) avec des [machines virtuelles Gen2](./linux/generation-2.md).

Les cas d'usage de la série D englobent les applications de classe Entreprise, les bases de données relationnelles, la mise en cache en mémoire et l'analytique.

## <a name="ddv4-series"></a>Série Ddv4

Les tailles de la série Ddv4 s'exécutent sur Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). La série Ddv4 offre une combinaison de processeurs virtuels, de mémoire et de disque temporaire adaptée à la plupart des charges de travail de production.

Les nouvelles tailles de machines virtuelles Ddv4 incluent un stockage SSD local rapide et plus volumineux (2 400 Gio maximum) et sont conçues pour les applications qui bénéficient d'un stockage local à faible latence et à haut débit, comme les applications qui nécessitent des lectures/écritures rapides sur un espace de stockage temporaire ou qui requièrent un espace de stockage temporaire pour les caches ou les fichiers temporaires. Vous pouvez associer les stockages SSD Standard et HDD Standard aux machines virtuelles Ddv4. Le stockage sur disque de données à distance est facturé séparément des machines virtuelles.

ACU : 195-210

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s | Nombre max. de cartes réseau/Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2/1 000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2/2 000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4/4 000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8/8 000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8/16 000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8/24 000 |
| Standard_D64d_v4 | 64 | 256 | 2 400 | 32 | 615000/3872 | 8/30 000 |

## <a name="ddsv4-series"></a>Série Ddsv4

La série Ddsv4 s'exécute sur Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). La série Ddsv4 offre une combinaison de processeurs virtuels, de mémoire et de disque temporaire adaptée à la plupart des charges de travail de production.

Les nouvelles tailles de machines virtuelles Ddsv4 incluent un stockage SSD local rapide et plus volumineux (2 400 Gio maximum) et sont conçues pour les applications qui bénéficient d'un stockage local à faible latence et à haut débit, comme les applications qui nécessitent des lectures/écritures rapides sur un espace de stockage temporaire ou qui requièrent un espace de stockage temporaire pour les caches ou les fichiers temporaires. 

 > [!NOTE]
 >Les tarifs et les compteurs de facturation de ces tailles sont identiques à ceux de la série Ddv4.

ACU : 195-210

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max. de cartes réseau/Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3000/48 | 2/1 000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6 400/96 | 2/2 000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12 800/192 | 4/4 000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25 600/384 | 8/8 000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51 200/768 | 8/16 000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76 800/1152 | 8/24 000 |
| Standard_D64ds_v4 | 64 | 256 | 2 400 | 32 | 615000/3872(1600) | 80 000/1 200 | 8/30 000 |

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
