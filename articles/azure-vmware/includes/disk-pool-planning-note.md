---
title: Remarque sur la planification du pool de disques pour un réseau virtuel
description: Remarque importante à propos de l’importance du déploiement d’un réseau virtuel plus proche des hôtes VMware Azure Solution.
ms.topic: include
ms.date: 07/14/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: ddee39189fd93d5651e8e879375b977a4222e9fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729119"
---
Si vous envisagez de mettre à l’échelle vos hôtes VMware Azure Solution à l’aide de [pools de disques Azure](../../virtual-machines/disks-pools.md), il est essentiel de déployer le réseau virtuel proche de vos ordinateurs hôtes avec une passerelle de réseau virtuel ExpressRoute. Plus le stockage est proche de vos hôtes, plus les performances sont élevées.
