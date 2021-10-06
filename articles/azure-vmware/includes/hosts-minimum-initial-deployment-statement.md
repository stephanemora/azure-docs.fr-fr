---
title: Déploiement d’hôtes minimal initial
description: Le déploiement initial minimal est de trois hôtes.
ms.topic: include
ms.date: 09/29/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 56a2f12ae7dddc6e0783d715554934d48dd227b1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300368"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

Pour chaque cloud privé créé, il existe un cluster vSAN par défaut. Vous pouvez ajouter, supprimer et mettre à l’échelle des clusters.  Le nombre minimal d’hôtes par cluster et pour le déploiement initial s’élève à trois. 

Vous utilisez vSphere et NSX-T Manager pour gérer la plupart des autres aspects de la configuration ou de l’exploitation du cluster. Tout le stockage local de chaque hôte dans un cluster est contrôlé par le logiciel vSAN.

>[!TIP]
>Vous pouvez toujours étendre le cluster et ajouter des clusters supplémentaires ultérieurement si vous devez aller au-delà du chiffre de déploiement initial.
