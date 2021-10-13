---
title: Remarque sur la planification du pool de disques pour un réseau virtuel
description: Remarque importante à propos de l’importance du déploiement d’un réseau virtuel plus proche des hôtes VMware Azure Solution.
ms.topic: include
ms.date: 07/14/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 04bd1b8b4faf697084dad49fa927394deaad3572
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638504"
---
Si vous envisagez de mettre à l’échelle vos hôtes VMware Azure Solution à l’aide de [pools de disques Azure](../../virtual-machines/disks-pools.md), il est essentiel de déployer le réseau virtuel proche de vos ordinateurs hôtes avec une passerelle de réseau virtuel ExpressRoute. Plus le stockage est proche de vos hôtes, plus les performances sont élevées.
