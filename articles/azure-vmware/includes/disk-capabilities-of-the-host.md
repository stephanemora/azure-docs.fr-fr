---
title: Capacités de disque des ordinateurs hôtes
description: Les hôtes utilisés pour créer ou mettre à l’échelle des clusters proviennent d’un pool isolé d’hôtes.
ms.topic: include
ms.date: 04/23/2021
ms.openlocfilehash: 7ee15fbfd668d5db24282a6e4de8e4dfc6639c3e
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945746"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-cloud-clusters.md -->


Les clusters Azure VMware Solution sont basés sur une infrastructure nue hyperconvergée. Le tableau suivant indique les capacités de mémoire RAM, de processeur et de disque de l’ordinateur hôte.

| Type d’hôte | UC   | RAM (Go)  | Niveau de cache du vSAN NVMe (To, RAW)  | Niveau de capacité du vSAN SSD (To, RAW)  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  Deux processeurs Intel 18 cœurs cadencés à 2,3 GHz  |     576      |                3.2               |                15,20               |

Les hôtes utilisés pour créer ou mettre à l’échelle des clusters proviennent d’un pool isolé d’hôtes. Ces hôtes ont passé des tests matériels et toutes leurs données ont été effacées de manière sécurisée. 