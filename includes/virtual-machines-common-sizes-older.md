---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: d89a9c4c4498e249dbfbd453ef9772d18ffd213f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550147"
---
Cette section fournit des informations sur les générations plus anciennes tailles de machines virtuelles. Ces tailles sont toujours pris en charge mais ne reçoit pas de capacité supplémentaire. Il existe des tailles plus récente ou sur un autres qui sont généralement disponibles. Reportez-vous à [machines virtuelles de tailles pour Windows dans Azure](../articles/virtual-machines/windows/sizes.md) ou [tailles des machines virtuelles Linux dans Azure](../articles/virtual-machines/linux/sizes.md) pour choisir la machine virtuelle tailles qui conviendra le mieux adaptées à vos besoins.  

Pour plus d’informations sur le redimensionnement d’une VM Linux, consultez [redimensionner une machine virtuelle de Linux à l’aide d’Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Si vous utilisez des machines virtuelles Windows et que vous préférez utiliser PowerShell, consultez [redimensionner une machine virtuelle Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>De base A  

**Recommandation de taille plus récente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles du niveau De base sont principalement destinées aux charges de travail de développement et aux autres applications qui ne requièrent pas d’équilibrage de charge ou de mise à l’échelle automatique, ou aux machines virtuelles utilisant beaucoup de mémoire.

|Taille - Taille\Nom | Processeurs virtuels |Mémoire|Cartes réseau (max)|Taille max. du disque temporaire |Bande passante disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (300 par disque)|
|---|---|---|---|---|---|---|
|0A0\Basic_A0|1|768 Mo|2| 20 Go|1|1 x 300|
|A1\Basic_A1|1|1,75 Go|2| 40 Go |2|2 x 300|
|A2\Basic_A2|2|3,5 Go|2| 60 Go|4|4 x 300|
|A3\Basic_A3|4|7 Go|2| 120 Go |8|8 x 300|
|A4\Basic_A4|8|14 Go|2| 240 Go |16|16 x 300|

<br>

### <a name="a-series"></a>Série A  

**Recommandation de taille plus récente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU : 50-100

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (HDD) : Gio | Disques de données max. | Débit de disque de données max. : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1 x 500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2 x 500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4 x 500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / 1 000 |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / 2 000 |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / 1 000 |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / 2 000 |

<sup>1</sup> La taille A0 est trop sollicitée sur le matériel physique. Pour cette taille spécifique uniquement, les autres déploiements de clients peuvent affecter les performances de la charge de travail en cours d’exécution. Les performances relatives sont décrites ci-dessous comme référence attendue, soumises à une variation approximative de 15 pour cent.

<br>

### <a name="a-series---compute-intensive-instances"></a>Série A - Instances de calcul intensif  

**Recommandation de taille plus récente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU : 225

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles A8 à A11 et celles de la série H sont également appelées *instances nécessitant beaucoup de ressources système*. Le matériel qui exécute ces tailles a été conçu et optimisé pour les applications nécessitant beaucoup de ressources système et réseau, notamment les applications en cluster pour des calculs complexes, la modélisation et les simulations. La série A8-A11 utilise un processeur Intel Xeon E5-2670 cadencé à 2,6 GHZ, et la série H un processeur Intel Xeon E5-2667 v3 cadencé à 3,2 GHz.  

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (HDD) : Gio | Disques de données max. | Débit de disque de données max. : E/S par seconde | Nombre max de cartes réseau|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup>Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR, qui garantit une très faible latence et une large bande passante.  

<br>

### <a name="d-series"></a>Série D  

**Recommandation de taille plus récente**: [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| D1 standard  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 500                 |
| D2 standard  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 000                     |
| D3 standard  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2 000                     |
| D4 standard  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4 000                     |

<sup>1</sup> famille de machines virtuelles peuvent s’exécuter sur un de l’unité centrale suivantes : 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou de 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D - à mémoire optimisée  

**Recommandation de taille plus récente**: [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| D11 standard | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 000                     |
| D12 standard | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2 000                     |
| D13 standard | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4 000                     |
| D14 standard | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 8000                |

<sup>1</sup> famille de machines virtuelles peuvent s’exécuter sur un de l’unité centrale suivantes : 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou de 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>Série DS  

**Recommandation de taille plus récente**: [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4 000 / 32 (43) |3 200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8 000 / 64 (86) |6 400 / 64 |2 / 1 000 |
| Standard_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2 000 |
| Standard_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4 000 |

<sup>1</sup> famille de machines virtuelles peuvent s’exécuter sur un de l’unité centrale suivantes : 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou de 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS - à mémoire optimisée  

**Recommandation de taille plus récente**: [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU : 160-250 <sup>1,2</sup>

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8 000 / 64 (72) |6 400 / 64 |2 / 1 000 |
| Standard_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2 000 |
| Standard_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4 000 |
| Standard_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série DS peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> famille de machines virtuelles peuvent s’exécuter sur un de l’unité centrale suivantes : 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou de 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>
