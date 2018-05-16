---
title: Azure Machine Learning et les FPGA
description: Découvrez comment accélérer des modèles et des réseaux neuronaux profonds à l’aide de FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Qu’est-ce qu’un FPGA (field programmable gate array) ?

Les FPGA sont des circuits intégrés qui peuvent,au besoin, être reconfigurés. Vous pouvez modifier un FPGA en fonction d’une nouvelle logique à implémenter. Les modèles d’accélération matérielle pour Azure Machine Learning permettent de déployer des modèles formés sur des FPGA dans le cloud Azure.

Cette fonctionnalité est optimisée par Project Brainwave qui gère la conversion des réseaux neuronaux profonds en programme FPGA. 

## <a name="why-use-an-fpga"></a>Pourquoi utiliser un FPGA ?

Les FPGA fournissent une latence extrêmement faible et sont particulièrement efficaces pour le scoring des données dans des petits lots (il n’y a pas d’exigence pour une grande taille de lot).  Ils sont économiques et disponibles dans Azure.  Project Brainwave peut mettre en parallèle des réseaux neuronaux profonds préformés sur ces FPGA pour une montée en charge de votre service.

## <a name="next-steps"></a>Étapes suivantes

[Déployer un modèle en tant que service web sur un FPGA](how-to-deploy-fpga-web-service.md)

[Découvrez comment installer le kit SDK FPGA](reference-fpga-package-overview.md)