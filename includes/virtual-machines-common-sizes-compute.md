---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 5a4495dd675b662273715b5c13a5594adc87fceb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333835"
---
<!-- F-series, Fs-series* -->

Les tailles de machine virtuelle optimisées pour le calcul ont un ratio processeur/mémoire élevé et conviennent aux serveurs web, appliances réseau, processus de traitement par lots et serveurs d’applications à trafic moyen. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement.

La série Fsv2 repose sur le processeur Intel® Xeon® Platinum 8168, avec une vitesse d’horloge turbo pour tous les noyaux de 3,4 GHz et une fréquence turbo monocœur maximale de 3,7 GHz. Les instructions Intel® AVX-512, une nouveauté sur les processeurs évolutifs Intel, fourniront jusqu'à 2X plus de performances pour les charges de travail de traitement vectoriel pour les opérations en virgule flottante simple ou double précision. En d’autres termes, elles sont très rapides pour les charges de travail de calcul. 

Affichant le coût le plus bas par heure, la série Fsv2 offre le meilleur rapport prix-performances de la gamme Azure si l’on considère les unités de calcul Azure (ACU) par processeur virtuel. 

La série F est basée sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz pouvant aller jusqu’à 3,1 GHz avec Intel Turbo Boost Technology 2.0. Ce sont les mêmes performances d’UC que la série Dv2 de machines virtuelles.  

Les machines virtuelles de la série F sont un excellent choix pour les charges de travail qui exigent des processeurs plus rapides, mais n’ont pas besoin d’autant de mémoire ou de stockage temporaire par processeur virtuel.  Les charges de travail telles que l’analyse, les serveurs de jeux, les serveurs web et le traitement par lots tireront avantage de la série F.

La série Fs propose tous les avantages de la série F, en plus du stockage Premium.

## <a name="fsv2-series-sup1sup"></a>Série Fsv2 <sup>1</sup>

ACU : 195 - 210

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

| Taille             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4 000 / 31 (32)           | 3 200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8 000 / 63 (64)           | 6 400 / 95                | 2 / 1,750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16 000 / 127 (128)        | 12 800 / 190              | 4 / 3,500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32 000 / 255 (256)        | 25 600 / 380              | 4 / 7,000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64 000 / 512 (512)        | 51 200 / 750              | 8 / 14,000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128 000 / 1 024 (1 024)     | 80 000 / 1 100             | 8 / 28,000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144 000 / 1 152 (1 520)     | 80 000 / 1 100             | 8 / 30 000              |


<sup>1</sup> Machines virtuelles de série Fsv2 dotées de la technologie Hyper-Threading d’Intel®

<sup>2</sup> Plus de 64 processeurs virtuels nécessitent l’un de ces systèmes d’exploitation invités pris en charge : Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 et Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 avec LIS 4.2.1

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.

## <a name="fs-series-sup1sup"></a>Série Fs <sup>1</sup>

ACU : 210 - 250

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4 000 / 32 (12) |3 200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8 000 / 64 (24) |6 400 / 96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12000 |

MBps = 10^6 octets par seconde, et Gio = 1024^3 octets.

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série Fs peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).


<br>

## <a name="f-series"></a>Série F

ACU : 210 - 250

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |


<br>


