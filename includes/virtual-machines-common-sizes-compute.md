---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 7bfb96a40dedf780920e6fdd6e08e1b583bea902
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501228"
---
<!-- F-series, Fs-series* -->

Les tailles de machine virtuelle optimisées pour le calcul ont un ratio processeur/mémoire élevé et conviennent aux serveurs web, appliances réseau, processus de traitement par lots et serveurs d’applications à trafic moyen. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement.

La série Fsv2 repose sur le processeur Intel® Xeon® Platinum 8168, avec une vitesse d’horloge turbo pour tous les noyaux de 3,4 GHz et une fréquence turbo monocœur maximale de 3,7 GHz. Les instructions Intel® AVX-512, une nouveauté sur les processeurs évolutifs Intel, fourniront jusqu'à 2X plus de performances pour les charges de travail de traitement vectoriel pour les opérations en virgule flottante simple ou double précision. En d’autres termes, elles sont très rapides pour les charges de travail de calcul. 

Affichant le coût le plus bas par heure, la série Fsv2 offre le meilleur rapport prix-performances de la gamme Azure si l’on considère les unités de calcul Azure (ACU) par processeur virtuel.

## <a name="fsv2-series-sup1sup"></a>Série Fsv2 <sup>1</sup>

ACU : 195 - 210

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

| Size             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4 000 / 31 (32)           | 3 200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8 000 / 63 (64)           | 6 400 / 95                | 2/1 750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16 000 / 127 (128)        | 12 800 / 190              | 4/3 500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32 000 / 255 (256)        | 25 600 / 380              | 4/7 000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64 000 / 512 (512)        | 51 200 / 750              | 8/14 000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96 000 / 768 (768)        | 76 800 / 1 100             | 8 / 21 000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128 000 / 1 024 (1 024)     | 80 000 / 1 100             | 8/28 000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144 000 / 1 152 (1 520)     | 80 000 / 1 100             | 8 / 30000              |

<sup>1</sup> Machines virtuelles de série Fsv2 dotées de la technologie Hyper-Threading d’Intel®

<sup>2</sup> Plus de 64 processeurs virtuels nécessitent l’un de ces systèmes d’exploitation invités pris en charge : Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 et Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 avec LIS 4.2.1

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.