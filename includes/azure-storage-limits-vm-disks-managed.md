---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/12/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8e9d04c7d374aabf3870a3260bc1f5d808b8ee9c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906307"
---
**Disques managés HDD Standard**

| Type de disque Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60 *             | S70 *             | S80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Taille du disque en Gio          | 32             | 64             | 128            | 256  | 512            | 1 024    | 2 048     | 4 095    | 8 192     | 16 384     | 32 767     |
| IOPS par disque       | Jusqu’à 500              | Jusqu’à 500              | Jusqu’à 500              | Jusqu’à 500 | Jusqu’à 500              | Jusqu’à 500              | Jusqu’à 500             | Jusqu’à 500              | Jusqu’à 1 300              | Jusqu’à 2 000              | Jusqu’à 2 000              |
| Débit par disque | Jusqu’à 60 Mio/s | Jusqu’à 60 Mio/s | Jusqu’à 60 Mio/s | Jusqu’à 60 Mio/s | Jusqu’à 60 Mio/s | Jusqu’à 60 Mio/s | Jusqu’à 60 Mio/s | Jusqu’à 60 Mio/s| Jusqu’à 300 Mio/s | Jusqu’à 500 Mio/s | Jusqu’à 500 Mio/s |

**Disques managés SSD Standard**

| Type de disque SSD Standard | E4                | E6                | E10               | E15               | E20             | E30              | E40              | E50              | E60 *             | E70 *             | E80 *             |
|------------------------|-------------------|-------------------|-------------------|-------------------|-----------------|------------------|------------------|------------------|-------------------|-------------------|-------------------|
| Taille du disque (Gio)       | 32                | 64                | 128               | 256               | 512             | 1 024            | 2 048            | 4 095            | 8 192             | 16 384            | 32 767            |
| IOPS par disque          | Jusqu’à 120         | Jusqu’à 240         | Jusqu’à 500         | Jusqu’à 500         | Jusqu’à 500       | Jusqu’à 500        | Jusqu’à 500        | Jusqu’à 500        | Jusqu’à 1 300       | Jusqu’à 2 000       | Jusqu’à 2 000       |
| Débit par disque    | Jusqu’à 25 Mo/s   | Jusqu’à 50 Mo/s   | Jusqu’à 60 Mo/s   | Jusqu’à 60 Mo/s   | Jusqu’à 60 Mo/s | Jusqu’à 60 Mo/s  | Jusqu’à 60 Mo/s  | Jusqu’à 60 Mo/s  | Jusqu’à 300 Mio/s | Jusqu’à 500 Mio/s | Jusqu’à 500 Mio/s |

**Disques managés SSD Premium : limites par disque**

| Type de disque Premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60 *             | P70 *             | P80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Taille du disque (Gio)           | 32             | 64             | 128            | 256  | 512            | 1 024    | 2 048     | 4 095    | 8 192     | 16 384     | 32 767     |
| IOPS par disque       | Jusqu’à 120 | Jusqu’à 240              | Jusqu’à 500              | Jusqu’à 1 100 | Jusqu’à 2 300              | Jusqu’à 5 000              | Jusqu’à 7 500             | Jusqu’à 7 500              | Jusqu’à 12 500              | Jusqu’à 15 000              | Jusqu’à 20 000              |
| Débit par disque | Jusqu’à 25 Mio/s | Jusqu’à 50 Mio/s | Jusqu’à 100 Mio/s | Jusqu’à 125 Mio/s | Jusqu’à 150 Mio/s | Jusqu’à 200 Mio/s | Jusqu’à 250 Mio/s | Jusqu’à 250 Mio/s| Jusqu’à 480 Mio/s | Jusqu’à 750 Mio/s | Jusqu’à 750 Mio/s |

**Disques managés SSD Premium : limites par machine virtuelle**

| Ressource | Limite par défaut |
| --- | --- |
| Nb max. d'E/S par seconde par machine virtuelle |80 000 E/S par seconde avec la machine virtuelle GS5 |
| Débit max. par machine virtuelle |2 000 Mo/s avec la machine virtuelle GS5 |
