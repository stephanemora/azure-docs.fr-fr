---
title: Capacités de disque des ordinateurs hôtes
description: Les hôtes utilisés pour créer ou mettre à l’échelle des clusters proviennent d’un pool isolé d’hôtes.
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 847f69950a47208b27976972da8dc3427727e774
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747629"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Les clusters Azure VMware Solution sont basés sur une infrastructure nue hyperconvergée. Le tableau suivant indique les capacités de mémoire RAM, de processeur et de disque de l’ordinateur hôte.

| Type d’hôte | UC   | RAM (Go)  | Niveau de cache du vSAN NVMe (To, RAW)  | Niveau de capacité du vSAN SSD (To, RAW)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  Deux processeurs Intel 18 cœurs cadencés à 2,3 GHz  |     576      |                3.2               |                15,20               |

Les hôtes utilisés pour créer ou mettre à l’échelle des clusters proviennent d’un pool isolé d’hôtes. Ces hôtes ont passé des tests matériels et toutes leurs données ont été effacées de manière sécurisée. 