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
ms.openlocfilehash: ac8686d0ea5704492bfc2e08972a2f70c9b34c43
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37906817"
---
Les machines virtuelles de la série H sont les machines virtuelles de calcul haute performance les plus récentes, destinées à répondre à des besoins de calcul de haut niveau, par exemple en relation avec la modélisation moléculaire et la dynamique des fluides. Les machines virtuelles à 8 et 16 processeurs virtuels reposent sur la technologie de processeur Intel Haswell E5-2667 V3 avec mémoire DDR4 et stockage temporaire SSD. 

En plus de la puissance substantielle du processeur, la série H offre différentes options pour des réseaux RDMA à faible latence, en utilisant FDR InfiniBand et plusieurs configurations de mémoire pouvant satisfaire des exigences de calcul nécessitant une mémoire conséquente.



## <a name="h-series"></a>Série H

ACU : 290-300

Stockage Premium : non pris en charge

Mise en cache du Stockage Premium : non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de disque max : E/S par seconde | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1 000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1 000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR, qui garantit une très faible latence et une large bande passante.

<br>






