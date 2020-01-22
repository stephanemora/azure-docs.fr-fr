---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 1867164954a3f9dff7a8a8c04e249a13edccb84a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020940"
---
Cette section fournit des informations sur les tailles de machines virtuelles des anciennes générations. Ces tailles sont toujours prises en charge mais ne peuvent pas recevoir de capacité supplémentaire. Il existe des tailles plus récentes ou alternatives qui sont généralement disponibles. Reportez-vous aux sections [Tailles des machines virtuelles Windows dans Azure](../articles/virtual-machines/windows/sizes.md) ou [Tailles des machines virtuelles Linux dans Azure](../articles/virtual-machines/linux/sizes.md) pour choisir les tailles de machine virtuelle qui seront les plus adaptées à vos besoins.  

Pour plus d’informations sur le redimensionnement d’une machine virtuelle Linux, consultez [Redimensionner une machine virtuelle Linux avec Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Si vous utilisez des machines virtuelles Windows et que vous préférez utiliser PowerShell, consultez [Redimensionner une machine virtuelle Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>De base A  

**Recommandation de taille plus récente** : [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles du niveau De base sont principalement destinées aux charges de travail de développement et aux autres applications qui ne requièrent pas d’équilibrage de charge ou de mise à l’échelle automatique, ou aux machines virtuelles utilisant beaucoup de mémoire.

|Taille - Taille\Nom | Processeurs virtuels |Mémoire|Cartes réseau (max)|Taille max. du disque temporaire |Bande passante disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (300 par disque)|
|---|---|---|---|---|---|---|
|0A0\Basic_A0|1|768 Mo|2| 20 Go|1|1 x 300|
|A1\Basic_A1|1|1,75 Go|2| 40 Go |2|2 x 300|
|A2\Basic_A2|2|3,5 Go|2| 60 Go|4|4 x 300|
|A3\Basic_A3|4|7 Go|2| 120 Go |8|8 x 300|
|A4\Basic_A4|8|14 Go|2| 240 Go |16|16 x 300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0-A4 à l’aide de l’interface de ligne de commande et de Powershell

Dans le modèle de déploiement classique, certains noms de tailles de machines virtuelles sont légèrement différents dans Powershell et dans l’interface de ligne de commande :

* Standard_A0 est ExtraSmall (Très petit)
* Standard_A1 est Small (Petit)
* Standard_A2 est Medium (Moyen)
* Standard_A3 est Large (Grand)
* Standard_A4 est ExtraLarge (Très grand)

### <a name="a-series"></a>Série A  

**Recommandation de taille plus récente** : [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU : 50-100

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (HDD) : Gio | Disques de données max. | Débit de disque de données max. : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps)  |
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

**Recommandation de taille plus récente** : [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU : 225

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles A8 à A11 et celles de la série H sont également appelées *instances nécessitant beaucoup de ressources système*. Le matériel qui exécute ces tailles a été conçu et optimisé pour les applications nécessitant beaucoup de ressources système et réseau, notamment les applications en cluster pour des calculs complexes, la modélisation et les simulations. La série A8-A11 utilise un processeur Intel Xeon E5-2670 cadencé à 2,6 GHZ, et la série H un processeur Intel Xeon E5-2667 v3 cadencé à 3,2 GHz.  

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (HDD) : Gio | Disques de données max. | Débit de disque de données max. : E/S par seconde | Nombre max de cartes réseau|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup>Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand QDR, qui garantit une très faible latence et une large bande passante.  

<br>

### <a name="d-series"></a>Série D  

**Recommandation de taille plus récente** : [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| D1 standard  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 500                 |
| D2 standard  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 000                     |
| D3 standard  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2 000                     |
| D4 standard  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4 000                     |

<sup>1</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D - à mémoire optimisée  

**Recommandation de taille plus récente** : [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| D11 standard | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 000                     |
| D12 standard | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2 000                     |
| D13 standard | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4 000                     |
| D14 standard | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 8000                |

<sup>1</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

### <a name="ds-series"></a>Série DS  

**Recommandation de taille plus récente** : [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4 000 / 32 (43) |3 200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8 000 / 64 (86) |6 400 / 64 |2 / 1 000 |
| Standard_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2 000 |
| Standard_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4 000 |

<sup>1</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS - à mémoire optimisée  

**Recommandation de taille plus récente** : [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU : 160-250 <sup>1,2</sup>

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8 000 / 64 (72) |6 400 / 64 |2 / 1 000 |
| Standard_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2 000 |
| Standard_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4 000 |
| Standard_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série DS peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d'informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

### <a name="ls-series"></a>Série Ls

La série Ls offre jusqu’à 32 processeurs virtuels, grâce à la [Famille de processeurs Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Cette série propose les mêmes performances de processeur que celles de la série G/GS, associées à 8 Gio de mémoire par processeur virtuel.

La série Ls ne prend pas en charge la création d’un cache local pour augmenter l’IOPS de façon à atteindre les performances des disques de données durables. Le débit élevé et les IOPS du disque local rendent les machines virtuelles de la série Ls idéales pour les magasins NoSQL tels qu’Apache Cassandra et MongoDB, qui répliquent les données sur plusieurs machines virtuelles pour assurer une persistance en cas de défaillance d’une seule machine virtuelle.

ACU : 180-240

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Non pris en charge
 
| Size          | Processeurs virtuels | Mémoire (Gio) | Stockage temporaire (Gio) | Disques de données max. | Débit de stockage temporaire maximal (IOPS / Mbits/s) | Débit de disque maximal sans mise en cache (IOPS / Mbits/s) | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20 000/200 | 5000 / 125  | 2 / 4 000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000 / 250 | 4 / 8 000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000 / 500 | 8 / 16 000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000 / 1600   | 40000 / 1000     | 8 / 20 000 | 

Le débit de disque maximal possible avec des machines virtuelles de la série Ls peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d'informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.

### <a name="gs-series"></a>Série GS 

ACU : 180 - 240 <sup>1</sup>

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5 000 / 125 |2 / 2 000 |
| Standard_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4 000 |
| Standard_GS3 |8 |112 |224 |32 |40 000 / 400 (1 056) |20 000 / 500 |4 / 8 000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2 112) |40 000 / 1 000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160 000 / 1 600 (4 224) |80 000 / 2 000 |8 / 20 000 |

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou MBps) avec une machine virtuelle de la série GS peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d'informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> L’instance est isolée sur un matériel dédié à un client unique.

<sup>3</sup> Tailles avec nombre de cœurs limité disponibles.

<br>

### <a name="g-series"></a>Série G

ACU : 180 - 240

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2 000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4 000                     |
| Standard_G3  | 8         | 112         | 1 536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8 000                |
| Standard_G4  | 16        | 224         | 3 072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6 144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20 000           |

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.
<br>
