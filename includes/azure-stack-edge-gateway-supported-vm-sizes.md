---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 05/12/2021
ms.author: alkohli
ms.openlocfilehash: f1e6ff675c7754b1eaa8705f335818053b80ef40
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846575"
---
La taille de machine virtuelle détermine la quantité de ressources de calcul (comme l’UC, le GPU et la mémoire) qui sont mises à la disposition de la machine virtuelle. Vous devez créer des machines virtuelles en utilisant une taille de machine virtuelle adaptée à la charge de travail. Même si toutes les machines vont s’exécuter sur le même matériel, leur taille ont des limites différentes pour l’accès au disque. Cela peut vous aider à gérer l’accès global au disque sur toutes vos machines virtuelles. Si une charge de travail augmente, vous pouvez également redimensionner une machine virtuelle existante.

La création des machines virtuelles suivantes est prise en charge sur votre appareil Azure Stack Edge.

### <a name="dv2-series"></a>Série Dv2
| Taille             | Processeurs virtuels  | Mémoire (Gio)  | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde)  | Débit de stockage temporaire max. (E/S par seconde)  | Débit de disque de données max. (E/S par seconde)  | Disques de données max.  | Nombre max de cartes réseau  |
|------------------|-------|---------------|---------------------|--------------------------------|-------------------------------------|----------------------------------|-----------------|-----------|
| **Standard_D1_v2**   | 1     | 3,5           | 50                  | 1 000                           | 3000                                | 500                              | 4               | 2         |
| **Standard_D2_v2**   | 2     | 7             | 100                 | 1 000                           | 6000                                | 500                              | 8               | 4         |
| **Standard_D3_v2**   | 4     | 14            | 200                 | 1 000                           | 12 000                               | 500                              | 16              | 4         |
| **Standard_D4_v2**   | 8     | 28            | 400                 | 1 000                           | 24 000                               | 500                              | 32              | 8         |
| **Standard_D5_v2**   | 16    | 56            | 800                 | 1 000                           | 48 000                               | 500                              | 64              | 8         |
| **Standard_D11_v2**  | 2     | 14            | 100                 | 1 000                           | 6000                                | 500                              | 8               | 2         |
| **Standard_D12_v2**  | 4     | 28            | 200                 | 1 000                           | 12 000                               | 500                              | 16              | 4         |
| **Standard_D13_v2**  | 8     | 56            | 400                 | 1 000                           | 24 000                               | 500                              | 32              | 8         |
| **Standard_D14_v2**  | 16    | 114           | 800                 | 1 000                           | 48 000                               | 500                              | 64              | 8         |


### <a name="dsv2-series"></a>Séries DSv2
| Taille              | Processeurs virtuels  | Mémoire (Gio)  | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde)  | Débit de stockage temporaire max. (E/S par seconde)  | Débit de disque de données max. (E/S par seconde)  | Disques de données max.  | Nombre max de cartes réseau  |
|-------------------|-------|---------------|---------------------|--------------------------------|-------------------------------------|----------------------------------|-----------------|-----------|
| **Standard_DS1_v2**   | 1     | 3,5           | 7                   | 2000                           | 4000                                | 2300                             | 4               | 2         |
| **Standard_DS2_v2**   | 2     | 7             | 14                  | 2000                           | 8000                                | 2300                             | 8               | 4         |
| **Standard_DS3_v2**   | 4     | 14            | 28                  | 2000                           | 16000                               | 2300                             | 16              | 4         |
| **Standard_DS4_v2**   | 8     | 28            | 56                  | 2000                           | 32000                               | 2300                             | 32              | 8         |
| **Standard_DS5_v2**   | 16    | 56            | 112                 | 2000                           | 64 000                               | 2300                             | 64              | 8         |
| **Standard_DS11_v2**  | 2     | 14            | 28                  | 2000                           | 8000                                | 2300                             | 8               | 2         |
| **Standard_DS12_v2**  | 4     | 28            | 56                  | 2000                           | 16000                               | 2300                             | 16              | 4         |
| **Standard_DS13_v2**  | 8     | 56            | 112                 | 2000                           | 32000                               | 2300                             | 32              | 8         |
| **Standard_DS14_v2**  | 16    | 114           | 224                 | 2000                           | 64 000                               | 2300                             | 64              | 8         |

Pour plus d’informations, consultez [Série Dv2 et DSv2](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>Série NCasT4_v3 (préversion)

Ces tailles sont prises en charge pour les machines virtuelles GPU sur votre appareil et sont optimisées pour les applications à accélération graphique gourmandes en calcul. Cette série est axée sur les charges de travail d’inférence comprenant le GPU Tesla T4 de Nvidia.

| Taille                  | Processeurs virtuels  | Mémoire (Gio)  | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde)  | Débit de stockage temporaire max. (E/S par seconde)  | Débit de disque de données max. (E/S par seconde)  | GPU  | Mémoire GPU (Gio)  | Nombre max de cartes réseau  |
|-----------------------|-------|---------------|---------------------|--------------------------------|-------------------------------------|----------------------------------|------|-------------------|-----------|
| **Standard_NC4as_T4_v3**  | 4     | 28            | 176                 | 2000                           | 48 000                               | 2300                             | 1    | 16                | 4         |
| **Standard_NC8as_T4_v3**  | 8     | 56            | 352                 | 2000                           | 48 000                               | 2300                             | 1    | 16                | 8         |

Pour plus d’informations, consultez [Série NCasT4_v3](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>Série F

Ces séries sont optimisées pour les charges de travail de calcul et s’exécutent sur des processeurs Intel Xeon. 

| Taille           | Processeurs virtuels  | Mémoire : Gio  | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde)  | Débit de stockage temporaire max. (E/S par seconde)  | Débit de disque de données max. (E/S par seconde)  | Disques de données max.  | Nombre max de cartes réseau  |
|----------------|---------|--------------|---------------------|--------------------------------|-------------------------------------|----------------------------------|-----------------|-----------|
| **Standard_F1**    | 1       | 2            | 16                  | 1 000                           | 3000                                | 500                              | 4               | 2         |
| **Standard_F2**    | 2       | 4            | 32                  | 1 000                           | 6000                                | 500                              | 8               | 4         |
| **Standard_F4**    | 4       | 8            | 64                  | 1 000                           | 12 000                               | 500                              | 16              | 4         |
| **Standard_F8**    | 8       | 16           | 128                 | 1 000                           | 24 000                               | 500                              | 32              | 8         |
| **Standard_F16**   | 16      | 32           | 256                 | 1 000                           | 48 000                               | 500                              | 64              | 8         |
| **Standard_F1s**   | 1       | 2            | 4                   | 2000                           | 4000                                | 2300                             | 4               | 2         |
| **Standard_F2s**   | 2       | 4            | 8                   | 2000                           | 8000                                | 2300                             | 8               | 4         |
| **Standard_F4s**   | 4       | 8            | 16                  | 2000                           | 16000                               | 2300                             | 16              | 4         |
| **Standard_F8s**   | 8       | 16           | 32                  | 2000                           | 32000                               | 2300                             | 32              | 8         |
| **Standard_F16s**  | 16      | 32           | 64                  | 2000                           | 64 000                               | 2300                             | 64              | 8         |

Pour plus d’informations, consultez [Série Fsv2](../articles/virtual-machines/fsv2-series.md).
