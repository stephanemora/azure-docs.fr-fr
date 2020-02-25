---
title: Série DC – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série DC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8f36220ab81c0fd9533bf2f025b801b37823da5f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492630"
---
# <a name="dc-series"></a>Série DC

La [série DC](#dc-series) est une famille de machines virtuelles dans Azure qui aide à protéger la confidentialité et l’intégrité de vos données et de votre code lors de leur traitement dans le cloud public. Ces machines bénéficient du processeur 3,7 GHz Intel XEON E-2176G avec la technologie SGX. Avec Intel Turbo Boost Technology, ces machines peuvent atteindre 4,7 GHz. Les instances de la série DC permettent aux clients de créer des applications sécurisées basées sur enclave pour protéger leur code et leurs données en cours d’utilisation.

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|---|---|---|---|---|---|---|---|
| Standard_DC2s | 2 | 8  | 100 | 2 | 4000 / 32 (43) | 3200 /48 | 2 / 1 500 |
| Standard_DC4s | 4 | 16 | 200 | 4 | 8000 / 64 (86) | 6400 /96 | 2 / 3 000 |

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