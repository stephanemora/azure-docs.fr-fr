---
title: Déploiement d’hôtes minimal initial
description: Le déploiement initial minimal est de trois hôtes.
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: 49eb2fdd105b02a52d0cba555a78e0d44d133bf6
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108179531"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->

Pour chaque cloud privé créé, il existe un cluster vSAN par défaut. Vous pouvez ajouter, supprimer et mettre à l’échelle des clusters.  Le nombre minimal d’hôtes par cluster et pour le déploiement initial s’élève à trois. 

Des clusters d’essai sont disponibles à des fins d’évaluation et limités à trois hôtes. Il existe un seul cluster d’évaluation par cloud privé, que vous pouvez mettre à l’échelle par hôte pendant la période d’évaluation.

Vous utilisez vSphere et NSX-T Manager pour gérer la plupart des autres aspects de la configuration ou de l’exploitation du cluster. Tout le stockage local de chaque hôte dans un cluster est contrôlé par le logiciel vSAN.

>[!TIP]
>Vous pouvez toujours étendre le cluster et ajouter des clusters supplémentaires ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.
