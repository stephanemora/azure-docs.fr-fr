---
title: Remarque sur la planification du pool de disques pour un réseau virtuel
description: Remarque importante à propos de l’importance du déploiement d’un réseau virtuel plus proche des hôtes VMware Azure Solution.
ms.topic: include
ms.date: 07/14/2021
ms.openlocfilehash: 39bcf823a71852396c1c05db4902abf000433499
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640566"
---
Si vous envisagez de mettre à l’échelle vos hôtes VMware Azure Solution à l’aide de [pools de disques Azure](../../virtual-machines/disks-pools.md), il est essentiel de déployer le réseau virtuel proche de vos ordinateurs hôtes avec une passerelle de réseau virtuel ExpressRoute.  Plus le stockage est proche de vos hôtes, plus les performances sont élevées.