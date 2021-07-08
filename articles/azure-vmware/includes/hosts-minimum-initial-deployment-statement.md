---
title: Déploiement d’hôtes minimal initial
description: Le déploiement initial minimal est de trois hôtes.
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: 32ed19a8ac3535f407549bea87acb6b433004b02
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111430319"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->

Pour chaque cloud privé créé, il existe un cluster vSAN par défaut. Vous pouvez ajouter, supprimer et mettre à l’échelle des clusters.  Le nombre minimal d’hôtes par cluster et pour le déploiement initial s’élève à trois. 

Des clusters d’essai sont disponibles à des fins d’évaluation et limités à trois hôtes par cloud privé.

Vous utilisez vSphere et NSX-T Manager pour gérer la plupart des autres aspects de la configuration ou de l’exploitation du cluster. Tout le stockage local de chaque hôte dans un cluster est contrôlé par le logiciel vSAN.

>[!TIP]
>Vous pouvez toujours étendre le cluster et ajouter des clusters supplémentaires ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.
