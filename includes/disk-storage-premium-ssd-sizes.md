---
title: Fichier Include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/24/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 759ce6ab44e7b50b88abc487cf2ab90041a06259
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112975254"
---
| Tailles de disque SSD Premium | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Taille du disque (Gio) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1 024 | 2 048 | 4 096 | 8 192 | 16 384 | 32 767 |
| IOPS provisionnées par disque | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2 300 | 5 000 | 7 500 | 7 500 | 16 000 | 18 000 | 20 000 |
| Débit provisionné par disque | 25 Mo/s | 25 Mo/s | 25 Mo/s | 25 Mo/s | 50 Mo/s | 100 Mo/s | 125 Mo/s | 150 Mo/s | 200 Mo/s | 250 Mo/s | 250 Mo/s| 500 Mo/s | 750 Mo/s | 900 Mo/s |
| Nb max. d’iOPS de rafale par disque | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 30 000* | 30 000* | 30 000* | 30 000* | 30 000* | 30 000* |
| Débit max. de rafale par disque | 170 Mo/s | 170 Mo/s | 170 Mo/s | 170 Mo/s | 170 Mo/s | 170 Mo/s | 170 Mo/s | 170 Mo/s | 1 000 Mo/sec* | 1 000 Mo/sec* | 1 000 Mo/sec* | 1 000 Mo/sec* | 1 000 Mo/sec* | 1 000 Mo/sec* |
| Durée maximale de rafale | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | Illimité* | Illimité* | Illimité* | Illimité* | Illimité* | Illimité* |
| Éligible pour la réservation | Non  | Non  | Non  | Non  | Non  | Non  | Non  | Non  | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an | Oui, jusqu’à un an |

\*S’applique uniquement aux disques avec la fonction de bursting à la demande activée.