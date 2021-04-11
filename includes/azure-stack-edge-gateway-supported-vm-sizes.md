---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
ms.openlocfilehash: f92dc476d3bc79fdb522424e36e44a13644c725e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078794"
---
La taille de machine virtuelle détermine la quantité de ressources de calcul (comme l’UC, le GPU et la mémoire) qui sont mises à la disposition de la machine virtuelle. Vous devez créer des machines virtuelles en utilisant une taille de machine virtuelle adaptée à la charge de travail. Même si toutes les machines vont s’exécuter sur le même matériel, leur taille ont des limites différentes pour l’accès au disque. Cela peut vous aider à gérer l’accès global au disque sur toutes vos machines virtuelles. Si une charge de travail augmente, vous pouvez également redimensionner une machine virtuelle existante.

La création des machines virtuelles suivantes est prise en charge sur votre appareil Azure Stack Edge.

### <a name="dv2-series"></a>Série Dv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)   | Disques de données max. | Nombre max de cartes réseau |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3,5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### <a name="dsv2-series"></a>Séries DSv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Disques de données max.| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3,5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64 000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


Pour plus d’informations, consultez [Série Dv2 et DSv2](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>Série NCasT4_v3 (préversion)

Ces tailles sont prises en charge pour les machines virtuelles GPU sur votre appareil et sont optimisées pour les applications à accélération graphique gourmandes en calcul. Cette série est axée sur les charges de travail d’inférence comprenant le GPU Tesla T4 de Nvidia. 

|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | GPU | Mémoire GPU (Gio) | Nombre max de cartes réseau |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

Pour plus d’informations, consultez [Série NCasT4_v3](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>Série F

Ces séries sont optimisées pour les charges de travail de calcul et s’exécutent sur des processeurs Intel Xeon. 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (Gio) |  Disques de données max. | Nombre max de cartes réseau |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

Pour plus d’informations, consultez [Série Fsv2](../articles/virtual-machines/fsv2-series.md).

