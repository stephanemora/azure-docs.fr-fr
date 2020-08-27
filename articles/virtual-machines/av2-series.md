---
title: Série Av2
description: Spécifications pour les machines virtuelles de la série Av2.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ac3f31fecd53ddf1dd8bb81229c67cb4bb60c34e
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650636"
---
# <a name="av2-series"></a>Série Av2

Les machines virtuelles de la série Av2 peuvent être déployées sur différents types de matériel et différents processeurs. Les machines virtuelles de la série Av2 affichent des performances d’unité centrale et des configurations de mémoire idéales pour les charges de travail de niveau d’entrée, propres au développement et au test. La taille est limitée afin d’offrir des performances de processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel sur lequel elle est déployée. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel à partir de la machine virtuelle. Voici quelques exemples de cas d’usage : serveurs de développement et de test, serveurs web à faible trafic, bases de données de petite et moyenne tailles, preuves de concept et dépôts de code.

ACU : 100

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge


| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS/Mbits/s en lecture/Mbits/s en écriture | Nombre de disques de données/débit max. : E/S par seconde | Nombre max de cartes réseau | Bande passante réseau attendue (Mbit/s)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1 000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2 000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4 000/80/40  | 8/8x500   | 4 | 1 000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8 000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2 000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4 000/80/40  | 8/8x500   | 4 | 1 000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8 000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Pour plus d’informations sur les types de disques : [Types de disques](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.