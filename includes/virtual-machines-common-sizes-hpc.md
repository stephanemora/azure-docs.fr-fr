---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: f3c1a946afb7f51a1c7543dabae7f2f42ff466be
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145822"
---
Azure série H virtual machines virtuelles sont conçus pour fournir des performances de votre implication dans la classe, l’évolutivité MPI et de rentabilité pour diverses charges de travail réelles HPC.

Les machines virtuelles de la série HB sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple de dynamique des fluides, d’analyse explicite par éléments finis et de modélisation du climat. Les machines virtuelles HB disposent de 60 cœurs de processeur AMD EPYC 7551, de 4 Go de RAM par cœur de processeur, et d’aucun hyperthreading. La plateforme AMD EPYC fournit plus de 260 Go/s de bande passante mémoire.

Machines virtuelles de série HC sont optimisés pour les applications pilotées par calcul dense, telles que l’analyse des éléments finis implicite, dynamics moléculaire et chimie. Les machines virtuelles HC disposent de 44 cœurs de processeur Intel Xeon Platinum 8168, de 8 Go de RAM par cœur de processeur, et d’aucun hyperthreading. La plateforme Intel Xeon Platinum prend en charge l’écosystème enrichi d’Intel outils logiciels tels que la Intel Math Kernel Library.

Chaud et machines virtuelles HC fonctionnalité 100 Go/s Mellanox EDR InfiniBand dans un fat non bloquant arborescence configuration pour des performances cohérentes RDMA. Chaud et machines virtuelles HC prend en charge les pilotes Mellanox/OFED standard tels que tous les types MPI et versions ainsi que des verbes RDMA, sont également pris en charge.

Machines virtuelles de série H sont optimisés pour les applications pilotées par haute fréquence du processeur ou mémoire volumineux par les exigences fondamentales. Intel Xeon E5 2667 v3 processeur de série H machines virtuelles fonctionnalité 8 ou 16 cœurs, 7 ou 14 Go de RAM par cœur de processeur et aucune l’hyperthreading. Fonctionnalités de la série H 56 Go/s Mellanox FDR InfiniBand dans un fat non bloquant arborescence configuration pour des performances cohérentes RDMA. Machines virtuelles de série H prennent en charge Intel MPI 5.x et MS-MPI.

## <a name="hb-series"></a>Série HB

Premium Storage : Prise en charge le stockage Premium la mise en cache : Pris en charge

| Taille | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante de mémoire GBIT/s | Fréquence de base du processeur (GHz) | Fréquence de All-cœurs (GHz, PIC) | Fréquence d’un cœur (GHz, PIC) | Performances RDMA (Go/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Cartes réseau Ethernet de max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Tous | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Série HC

Premium Storage : Prise en charge le stockage Premium la mise en cache : Pris en charge


| Taille | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante de mémoire GBIT/s | Fréquence de base du processeur (GHz) | Fréquence de All-cœurs (GHz, PIC) | Fréquence d’un cœur (GHz, PIC) | Performances RDMA (Go/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Cartes réseau Ethernet de max |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Tous | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Série H

ACU : 290-300

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante de mémoire GBIT/s | Fréquence de base du processeur (GHz) | Fréquence de All-cœurs (GHz, PIC) | Fréquence d’un cœur (GHz, PIC) | Performances RDMA (Go/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Cartes réseau Ethernet de max |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1 000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1 000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR, qui garantit une très faible latence et une large bande passante.

<br>
