---
title: Déploiement d’hôtes minimal initial
description: Le déploiement initial minimal est de trois hôtes.
ms.topic: include
ms.date: 04/27/2021
ms.openlocfilehash: 72f0ad6c02c7ef24301276f1c6f68b300c86c0a4
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108126305"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->


Pour chaque cloud privé créé, il existe un cluster vSAN par défaut. Vous pouvez ajouter, supprimer et mettre à l’échelle des clusters.  Le nombre minimal d’hôtes par cluster est trois. Vous pouvez ajouter des hôtes supplémentaires un par un, sans dépasser 16 hôtes par cluster. Le nombre maximal de clusters par cloud privé est 12.  Le déploiement initial d’Azure VMware Solution a trois hôtes. 

Des clusters d’essai sont disponibles à des fins d’évaluation et limités à trois hôtes. Il existe un cluster d’évaluation unique par cloud privé. Vous pouvez mettre à l’échelle un cluster d’évaluation à l’aide d’un seul hôte au cours de la période d’évaluation.

Vous utilisez vSphere et NSX-T Manager pour gérer la plupart des autres aspects de la configuration ou de l’exploitation du cluster. Tout le stockage local de chaque hôte dans un cluster est contrôlé par le logiciel vSAN.

>[!TIP]
>Vous pouvez toujours étendre le cluster et ajouter des clusters supplémentaires ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.