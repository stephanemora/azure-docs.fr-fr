---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76837559"
---
| Tailles de disque SSD Premium | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Taille du disque (Gio) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1 024 | 2 048 | 4 096 | 8 192 | 16 384 | 32 767 |
| IOPS par disque | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2 300 | 5 000 | 7 500 | 7 500 | 16 000 | 18 000 | 20 000 |
| Débit par disque | 25 Mio/s | 25 Mio/s | 25 Mio/s | 25 Mio/s | 50 Mio/s | 100 Mio/s | 125 Mio/s | 150 Mio/s | 200 Mio/s | 250 Mio/s | 250 Mio/s| 500 Mio/s | 750 Mio/s | 900 Mio/s |
| Nb max. d’iOPS de rafale par disque** | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 |
| Débit max. de rafale par disque** | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s | 170 Mio/s |
| Durée maximale de rafale** | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |
| Éligible pour la réservation | Non  | Non  | Non  | Non  | Non  | Non  | Non  | Non  | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an |

\*Indique une taille de disque actuellement en préversion. Pour plus d’informations sur la disponibilité régionale, voir [Nouvelles tailles de disque : managés et non managés](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).  
\*\*Désigne une fonctionnalité en préversion. Pour plus d’informations, consultez [Mode rafale](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability).
