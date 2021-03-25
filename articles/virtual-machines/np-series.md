---
title: Série NP - Machines virtuelles Azure
description: Spécifications pour les machines virtuelles de la série NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 09adb19623ea866091e1b949e78263661eddbb52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551145"
---
# <a name="np-series-preview"></a>Série NP (préversion) 
Les machines virtuelles de la série NP sont alimentées par des FPGA [Xilinx U250](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) pour accélérer les charges de travail, notamment l’inférence machine learning, le transcodage vidéo et la recherche de base de données et Analytics. Les machines virtuelles de la série NP sont également alimentées par des processeurs Intel Xeon 8171M (Skylake) avec une vitesse d’horloge de Turbo de 3,2 GHz.

Soumettez une requête à l’aide du [formulaire de préversion](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR9x_QCQkJXxHl4qOI4jC9YtUOVI0VkgwVjhaTFFQMTVBTDFJVFpBMzJSSCQlQCN0PWcu) pour participer au programme de préversion de la série NP.


[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
Génération de machine virtuelle prise en charge : Génération 1<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | FPGA | Mémoire FPGA : Gio | Disques de données max. | Nombre max. de cartes réseau/Bande passante réseau attendue (Mbits/s) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge
Consultez les [notes de publication de Xilinx Runtime (XRT)](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) pour obtenir la liste complète des systèmes d’exploitation pris en charge.

Au cours de la version préliminaire, les équipes d’ingénierie Microsoft Azure partageront des instructions spécifiques pour l’installation du pilote.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
