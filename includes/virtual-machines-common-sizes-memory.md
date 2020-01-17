---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 09f379279a7247f87b9e0830414a5e4363f41cdb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467524"
---
Les tailles de machine virtuelle à mémoire optimisée offrent un ration mémoire/processeur supérieur pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement.

* La série Ev3 dispose des processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) dans une configuration hyperthread, ce qui lui permet d’offrir ce qui se fait de mieux pour les charges de travail à usage général et d’aligner la série Ev3 sur les machines virtuelles à usage général que l’on retrouve dans la plupart des autres clouds.  La mémoire a été étendue (de 7 Gio/vCPU à 8 Gio/vCPU) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading.  La série Ev3 constitue la suite des tailles de machines virtuelles à mémoire haute des familles D/Dv2.

* Les machines de séries Eav4 et Easv4 utilisent le processeur EPYC<sup>TM</sup> 7452 2,35 Ghz d’AMD dans une configuration multithread avec un cache L3 jusqu’à 256 Mo, ce qui améliore les options d’exécution de la plupart des charges de travail à mémoire optimisée.  Les séries Eav4 et Easv4 ont les mêmes configurations de mémoire et de disque que les séries Ev3 et Esv3.

* La série Mv2 offre le nombre de processeurs virtuels le plus élevé (jusqu’à 416 processeurs virtuels) et la plus grande mémoire (jusqu’à 8,19 Tio) parmi toutes les machines virtuelles dans le cloud. Elle est idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.

* La série M propose un nombre élevé de processeurs virtuels (jusqu’à 128 processeurs virtuels) et une grande quantité de mémoire (jusqu’à 3,8 Tio). Elle est également idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.

* Les séries Dv2, G et DSv2/GS sont idéales pour les applications qui exigent des processeurs virtuels plus rapides, de meilleures performances de stockage temporaire, ou qui ont des exigences de mémoire plus élevées. Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.

* La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. La série Dv2 est environ 35 % plus rapide que la série D. Elle s’exécute sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

* Le Calcul Azure propose des tailles de machines virtuelles qui sont isolées pour un type de matériel spécifique et dédiées à un seul et même client.  Ces tailles de machines virtuelles conviennent mieux aux charges de travail qui nécessitent un niveau élevé d’isolation par rapport aux autres clients pour les charges de travail qui impliquent des éléments tels que les exigences réglementaires et de conformité.  Les clients peuvent également choisir de subdiviser les ressources de ces machines virtuelles isolées à l’aide de la [prise en charge d’Azure pour les machines virtuelles imbriquées](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Consultez les tableaux des familles de machines virtuelles ci-dessous pour connaître les options de machine virtuelle isolée.

## <a name="esv3-series"></a>Série Esv3

ACU : 160-190 <sup>1</sup>

Premium Storage : Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Les instances de la série ESv3 sont dotées des processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) et peuvent atteindre 3,5 GHz avec la technologie Intel Turbo Boost 2.0 et utiliser le stockage Premium. Les tailles des machines virtuelles de la série ESv3 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.


| Size             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1 000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2 000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4/4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16 000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96 000/768 (1 200)                                                   | 76800 / 1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> Machines virtuelles de série Esv3 dotées de la technologie Hyper-Threading d’Intel®.

<sup>2</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.

## <a name="easv4-series"></a>Série Easv4

ACU : 230 - 260

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Les tailles de la série Easv4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 de 2,35 Ghz, qui peut atteindre une fréquence maximale renforcée de 3,35 GHz et utiliser un SSD Premium. Les tailles des machines virtuelles de la série Easv4 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max. de cartes réseau / Bande passante réseau attendue (Mbits/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1 000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2 000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4/4000 |
| Standard_E16as_v4|16|128|256|32|32 000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64 000 / 510 (800)|51200 / 768|8 / 16 000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | | 
| Standard_E64as_v4 <sup>**</sup> |64|512|1 024|32| | | 
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  Ces tailles sont en préversion. Si vous souhaitez essayer ces grandes tailles, inscrivez-vous à l’adresse [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="ev3-series"></a>Série Ev3 

ACU : 160 - 190 <sup>1</sup>

Premium Storage : Non pris en charge

Mise en cache du Stockage Premium : Non pris en charge

Les instances de la série Ev3 sont dotées des processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) et peuvent atteindre 3,5 GHz avec la technologie Intel Turbo Boost 2.0. Les tailles des machines virtuelles de la série ESv3 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.

Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format ESv3. Les tarifs et compteurs de facturation pour les tailles ESv3 sont identiques à ceux de la série Ev3. 


| Size            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Cartes réseau (max)/Bande passante réseau |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1 000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2 000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4/4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16 000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1 600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1 600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Machines virtuelles de série Ev3 dotées de la technologie Hyper-Threading d’Intel®.

<sup>2</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.

## <a name="eav4-series"></a>Série Eav4

ACU : 230 - 260

Premium Storage : Non pris en charge

Mise en cache du Stockage Premium : Non pris en charge

Les tailles de la série Eav4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 de 2,35 Ghz, qui peut atteindre une Fmax renforcée de 3,35 GHz et utiliser le stockage Premium. Les tailles des machines virtuelles de la série Eav4 sont idéales pour les applications d’entreprise nécessitant une mémoire importante. Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser un SSD Premium, utilisez les tailles de la série Easv4. Les tarifs et les compteurs de facturation pour les tailles Easv4 sont identiques à celles de la série Eav3.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Nombre max. de cartes réseau / Bande passante réseau attendue (Mbits/s) |
| -----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1 000 |
| Standard\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2 000 |
| Standard\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4/4000 |
| Standard\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standard\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Standard\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16 000 |
| Standard\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standard\_E64a\_v4 <sup>**</sup> |64|512|1 600|32| | |
| Standard\_E96a\_v4 <sup>**</sup> |96|672|2 400|32| | |

<sup>**</sup>  Ces tailles sont en préversion.  Si vous souhaitez essayer ces grandes tailles, inscrivez-vous à l’adresse [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="mv2-series"></a>Série Mv2

ACU : 188-280<sup>1</sup>

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Accélérateur d’écriture : [Pris en charge](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

La série Mv2 offre une plateforme à haut débit et faible latence sur un processeur Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) à technologie Hyper-thread avec une fréquence de base principale de 2,5 GHz et une fréquence turbo max de 3,8 GHz. Quelle que soit leur taille, toutes les machines virtuelles de la série Mv2 peuvent utiliser des disques persistants Standard et Premium. Les instances de série Mv2 sont des tailles de machine virtuelle à mémoire optimisée fournissant des performances de calcul inégalées pour prendre en charge des bases de données et des charges de travail en mémoire volumineuses, avec un ratio mémoire/processeur élevé idéal pour les serveurs de base de données relationnelle, les caches volumineux et l’analytique en mémoire.

|Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>2</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>2</sup> | 208 | 2 850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M416ms_v2<sup>2, 3</sup> | 416 | 11400 | 8 192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>2, 3</sup> | 416 | 5700 | 8 192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Machines virtuelles de série Mv2 dotées de la technologie Hyper-Threading d’Intel®

<sup>2</sup> Les machines virtuelles de série Mv2 sont de la 2e génération uniquement. Si vous utilisez Linux, consultez [Support des machines virtuelles de 2e génération dans Azure](../articles/virtual-machines/linux/generation-2.md) pour obtenir des instructions sur la façon de rechercher et sélectionner une image.

<sup>3</sup> Pour les tailles M416ms_v2 et M416s_v2, notez qu’il existe une prise en charge initiale de l’image suivante uniquement : « GEN2 : SUSE Linux Enterprise Server (SLES) 12 SP4 pour applications SAP. »

## <a name="m-series"></a>Série M 

ACU : 160-180 <sup>1</sup>

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Les tailles de la série M sont basées sur le processeur Intel(R) Xeon(R) E7-8890 v3 @ 2,50 GHz   

Accélérateur d’écriture :  [Pris en charge](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000 / 125 | 4 / 2 000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8 / 4 000 |
| Standard_M32ts | 32 | 192    | 1 024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1 024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1 024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1 024   | 2 048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16 000          |
| Standard_M64ls  | 64 | 512    | 2 048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2 048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2 048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1 024 | 7168  | 64 | 80000 / 800 (1228) | 40000 / 1000 | 8 / 16 000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000 / 800 (1228) | 40000 / 1000 | 8 / 16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2 048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> Machines virtuelles de série M dotées de la technologie Hyper-Threading d’Intel®

<sup>2</sup> Les machines virtuelles avec plus de 64 processeurs virtuels nécessitent l’un de ces systèmes d’exploitation invités pris en charge : Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 et Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 avec LIS 4.2.1.

<sup>3</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>4</sup> L’instance est isolée sur un matériel dédié à un client unique.
<br>


## <a name="dsv2-series-11-15"></a>Série DSv2 11-15

ACU : 210 - 250 <sup>1</sup>

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Les tailles de la série DSv2 s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5 2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou MBps) avec une machine virtuelle de la série DSv2 peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d'informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> L’instance est isolée sur le matériel Intel Haswell et dédiée à un seul client.  
<sup>3</sup> Tailles avec nombre de cœurs limité disponibles.  
<sup>4</sup> 25 000 MBps avec mise en réseau accélérée. 

<br>

## <a name="dv2-series-11-15"></a>Série Dv2 11-15

ACU : 210 - 250

Premium Storage : Non pris en charge

Mise en cache du Stockage Premium : Non pris en charge

Les tailles de la série DSv2 s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5 2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Size              | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1 000          | 60000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.  
<sup>2</sup> 25 000 MBps avec mise en réseau accélérée. 
