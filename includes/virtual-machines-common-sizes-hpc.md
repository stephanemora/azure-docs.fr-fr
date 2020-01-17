---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 006a44e42ed209b6f0e614b92f97e43ec30b99ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467881"
---
Les machines virtuelles optimisées Azure HPC sont conçues pour offrir des performances, une extensibilité MPI et une rentabilité exceptionnelles adaptées à un vaste éventail d’applications réelles.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>Mise en réseau InfiniBand pour HPC à grande échelle
Les machines virtuelles HBv2 disposent d’un bus InfiniBand Mellanox HDR à 200 Gb/s, tandis que les machines virtuelles HB et HC disposent d’un bus InfiniBand Mellanox EDR à 100 Gb/s. Chacun de ces types de machine virtuelle est connecté dans une arborescence FAT non bloquante pour des performances RDMA optimisées et cohérentes.

Les machines virtuelles HBv2 prennent en charge le routage adaptatif et le transport connecté dynamique (DCT, en plus des transports RC et UD standards). Ces fonctionnalités améliorent les performances, l’extensibilité et la cohérence des applications, et leur utilisation est fortement recommandée.  

Les machines virtuelles HBv2, HB et HC prennent en charge les pilotes Mellanox/OFED standards. Par conséquent, tous les verbes RDMA et les types MPI sont pris en charge. Chacun de ces types de machines virtuelles supporte également le déchargement matériel des collectifs MPI pour accroître les performances de l’application.
 
Les machines virtuelles d’origine de la série H sont dotées de la technologie Mellanox FDR InfiniBand à 56 Gb/s. Pour tirer parti de l’interface InfiniBand sur les machines virtuelles de la série H, les clients doivent utiliser des images officiellement prises en charge et spécifiques à ce type de machine virtuelle sur la Place de marché Azure. 


## <a name="hbv2-series"></a>Série HBv2
Les machines virtuelles de la série HBv2 sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple la dynamique des fluides, l’analyse par éléments finis et la simulation de réservoir. Les machines virtuelles HBv2 disposent de 120 cœurs de processeur AMD EPYC 7742, de 4 Go de RAM par cœur de processeur, et d’aucun multithreading simultané. Chaque machine virtuelle HBv2 fournit jusqu’à 340 Go/s de bande passante de mémoire et jusqu’à 4 téraflops de calcul FP64. 

Premium Storage : Prise en charge

| Size | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Cartes réseau Ethernet max. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | Tous | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>Série HB
Les machines virtuelles de la série HB sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple la dynamique des fluides, l’analyse explicite par éléments finis et la modélisation du climat. Les machines virtuelles HB disposent de 60 cœurs de processeur AMD EPYC 7551, de 4 Go de RAM par cœur de processeur, et d’aucun multithreading simultané. Une machine virtuelle HB fournit jusqu’à 260 Go/s de bande passante de mémoire.  

ACU : 199-216

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

| Size | Processeurs virtuels | Processeur | Mémoire (Gio) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Gio) | Disques de données max. | Cartes réseau Ethernet max. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2 | 2.55 | 2.55 | 100 | Tous | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Série HC
Les machines virtuelles de la série HC sont optimisées pour les applications tributaires d’un calcul dense, telles que l’analyse implicite par éléments finis, la dynamique moléculaire et la chimie numérique. Les machines virtuelles HC disposent de 44 cœurs de processeur Intel Xeon Platinum 8168, de 8 Go de RAM par cœur de processeur, et d’aucun hyperthreading. La plateforme Intel Xeon Platinum prend en charge le riche écosystème d’outils logiciels d’Intel, comme Intel Math Kernel Library. 

ACU : 297-315

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge


| Size | Processeurs virtuels | Processeur | Mémoire (Gio) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Gio) | Disques de données max. | Cartes réseau Ethernet max. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Tous | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Série H
Les machines virtuelles de la série H sont optimisées pour les applications tributaires de fréquences de processeur élevées ou de besoins en mémoire important par cœur. Les machines virtuelles de la série H disposent de 8 ou 16 cœurs de processeur Intel Xeon E5 2667 v3, de 14 Go de RAM par cœur de processeur, et d’aucun hyperthreading. Une machine virtuelle de la série H fournit jusqu’à 80 Go/s de bande passante de mémoire.

ACU : 290-300

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size | Processeurs virtuels | Processeur | Mémoire (Gio) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Gio) | Disques de données max. | Cartes réseau Ethernet max. |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR.

<br>
