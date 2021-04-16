---
title: Fichier include
description: Fichier include
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504682"
---
| SKU de la passerelle | Connexions par seconde | Nombre de flux | Mégabits par seconde | Paquets par seconde | Bande passante du circuit | Nombre de routes publiées par passerelle (vers MSEE) | Nombre de routes apprises par passerelle (depuis MSEE) | Nombre de machines virtuelles dans le réseau virtuel |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Référence (SKU) de base (déconseillée)** | N/A | N/A | 500 | N/A | N/A | N/A | N/A | N/A |
| **Référence Standard/ErGw1AZ** | 7 000 | 400 000 | > 1 000 | > 100 000 | 1 Gbit/s |  500 | 4 000 | 2 000 (réduits à 1 000 pendant la maintenance, restaurés par la suite.) | 
| **Référence Hautes performances/ErGw2AZ** | 14 000 | 840 000 | > 2 000 | 250 000 | 1 Gbit/s | 500 | ~9 500 (réduire à 4 000 si plus de 6 500 machines virtuelles se trouvent dans le réseau virtuel.) | 4 500 |
| **Référence Ultra-Performance/ErGw3AZ** | 16 000 | 950 000 | ~10 000 | 1 000 000 | 1 Gbit/s | 500 | ~9 500 | 11 000 |
