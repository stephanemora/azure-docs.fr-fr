---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: a8f0e61a953a2e2471e49d571063f6202b7ab76d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540480"
---
Les machines virtuelles de la série H sont les machines virtuelles de calcul haute performance les plus récentes, destinées à gérer des charges de travail tels que le traitement par lots, l’analytique, la modélisation moléculaire et la dynamique des fluides. Les machines virtuelles à 8 et 16 processeurs virtuels reposent sur la technologie de processeur Intel Haswell E5-2667 V3 avec mémoire DDR4 et stockage temporaire SSD. 

En plus de la puissance substantielle du processeur, la série H offre différentes options pour des réseaux RDMA à faible latence, en utilisant FDR InfiniBand et plusieurs configurations de mémoire pouvant satisfaire des exigences de calcul nécessitant une mémoire conséquente.



## <a name="h-series"></a>Série H

ACU : 290-300

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de disque max : E/S par seconde | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1 000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1 000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR, qui garantit une très faible latence et une large bande passante.

<br>






