---
title: Capacités de disque des ordinateurs hôtes
description: Les hôtes utilisés pour créer ou mettre à l’échelle des clusters proviennent d’un pool isolé d’hôtes.
ms.topic: include
ms.date: 04/23/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 29a16c506d4e381e25c9c12a424b3e1fba7ad08d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638508"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Les clusters Azure VMware Solution sont basés sur une infrastructure nue hyperconvergée. Le tableau suivant indique les capacités de mémoire RAM, de processeur et de disque de l’ordinateur hôte.

| Type d’hôte | UC   | RAM (Go)  | Niveau de cache du vSAN NVMe (To, RAW)  | Niveau de capacité du vSAN SSD (To, RAW)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  Deux processeurs Intel 18 cœurs cadencés à 2,3 GHz  |     576      |                3.2               |                15,20               |

Les hôtes utilisés pour créer ou mettre à l’échelle des clusters proviennent d’un pool isolé d’hôtes. Ces hôtes ont passé des tests matériels et toutes leurs données ont été effacées de manière sécurisée. 