---
title: Série NCas T4 v3
description: Spécifications pour les machines virtuelles de la série NCas T4 v3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: d07da12ecef7dfc6cf1a6df67f6beae01c4573d9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168114"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3-series 

Les machines virtuelles de la série NCasT4_v3 sont alimentées par [processeurs graphiques NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) et des processeurs AMD EPYC 7V12(Rome). Les machines virtuelles comportent jusqu’à 4 processeurs graphiques NVIDIA T4 avec 16 Go de mémoire chacun, jusqu’à 64 cœurs AMD EPYC 7V12 (Rome) et 440 Gio de mémoire système. Ces machines virtuelles sont idéales pour exécuter des charges de travail de ML et d’IA en utilisant CUDA, TensorFlow, Pytorch, Caffe et d’autres frameworks ou les charges de travail graphiques à l’aide de la technologie GRID de NVIDIA. La série NCasT4_v3 est idéale pour l’exécution de charges de travail d’inférence.

<br>

ACU : 230-260

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Migration dynamique : Non pris en charge

Mises à jour avec préservation de la mémoire : Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NV8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NV16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NV64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

Pour tirer parti des fonctionnalités GPU des machines virtuelles de série NCasT4_V3 Azure sous Windows ou Linus, des pilotes graphiques NVIDIA doivent être installés.

Pour installer manuellement les pilotes graphiques NVIDIA, consultez [Installer les pilotes GPU NVIDIA sur les machines virtuelles de série N exécutant Windows](./windows/n-series-driver-setup.md) pour connaître les systèmes d’exploitation pris en charge, les pilotes et les étapes d’installation et de vérification.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
