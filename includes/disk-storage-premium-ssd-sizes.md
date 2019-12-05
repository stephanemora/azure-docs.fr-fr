---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52dbf03c7d4c2c2de565d6793fc867cf5b886db3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "73991042"
---
| Tailles de disque SSD Premium | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Taille du disque (Gio) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1 024 | 2 048 | 4 096 | 8 192 | 16 384 | 32 767 |
| IOPS par disque | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2 300 | 5 000 | 7 500 | 7 500 | 16 000 | 18 000 | 20 000 |
| Débit par disque | 25 Mio/s | 25 Mio/s | 25 Mio/s | 25 Mio/s | 50 Mio/s | 100 Mio/s | 125 Mio/s | 150 Mio/s | 200 Mio/s | 250 Mio/s | 250 Mio/s| 500 Mio/s | 750 Mio/s | 900 Mio/s |
| Nb max. d’iOPS de rafale par disque** | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 |
| Débit max. de rafale par disque** | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s |
| Durée maximale de rafale** | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*Indique une taille de disque actuellement en préversion. pour plus d’informations sur la disponibilité régionale, consultez [Nouvelles tailles de disque : managés et non managés](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\**Désigne une fonctionnalité actuellement en préversion. pour plus d’informations, consultez [rafales de disque](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability).
