---
title: Déploiement d’hôtes minimal initial
description: Le déploiement initial minimal est de trois hôtes.
ms.topic: include
ms.date: 04/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d0efae1a8f3bf30360d2649b9d638f5cc800179f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747620"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

Pour chaque cloud privé créé, il existe un cluster vSAN par défaut. Vous pouvez ajouter, supprimer et mettre à l’échelle des clusters.  Le nombre minimal d’hôtes par cluster et pour le déploiement initial s’élève à trois. 

Des clusters d’essai sont disponibles à des fins d’évaluation et limités à trois hôtes par cloud privé.

Vous utilisez vSphere et NSX-T Manager pour gérer la plupart des autres aspects de la configuration ou de l’exploitation du cluster. Tout le stockage local de chaque hôte dans un cluster est contrôlé par le logiciel vSAN.

>[!TIP]
>Vous pouvez toujours étendre le cluster et ajouter des clusters supplémentaires ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.
