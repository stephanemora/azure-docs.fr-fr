---
title: Série HB
description: Spécifications pour les machines virtuelles de la série HB.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: b0ffd9af8df830c1bdacbfe3809d4086346679b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678560"
---
# <a name="hb-series"></a>Série HB

Les machines virtuelles de la série HB sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple la dynamique des fluides, l’analyse explicite par éléments finis et la modélisation du climat. Les machines virtuelles HB disposent de 60 cœurs de processeur AMD EPYC 7551, de 4 Go de RAM par cœur de processeur, et d’aucun multithreading simultané. Une machine virtuelle HB fournit jusqu’à 260 Go/s de bande passante de mémoire.

ACU : 199-216

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Migration dynamique : Non pris en charge

Mises à jour avec préservation de la mémoire : Non pris en charge

| Taille | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Cartes réseau Ethernet max. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2 | 2.55 | 2.55 | 100 | Tous | 700 | 4 | 1 |

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