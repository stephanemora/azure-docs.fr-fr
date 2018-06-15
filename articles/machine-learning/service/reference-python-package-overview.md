---
title: Packages Python pour Azure Machine Learning
description: Découvrez les packages python disponibles pour les utilisateurs d’Azure Machine Learning.
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7a8f9e9d757aaab04706dd3b7dc2b962b3a04130
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833262"
---
# <a name="python-packages-for-azure-machine-learning"></a>Packages Python pour Azure Machine Learning

Découvrez les packages Python propriétaires de Microsoft pour Azure Machine Learning. Vous pouvez utiliser ces bibliothèques et ces fonctions conjointement à d’autres packages tiers ou open source mais, pour utiliser ces packages propriétaires, votre code Python doit s’exécuter sur un service ou un ordinateur qui fournit les interpréteurs.

Les packages Azure Machine Learning sont des **extensions pip-installable de Python pour Azure Machine Learning** qui permettent aux scientifiques des données et aux développeurs IA de générer et de déployer rapidement des modèles d’apprentissage automatique et d’apprentissage profond extrêmement précis dans divers domaines.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Package Azure ML pour Vision par ordinateur

Avec le package Azure ML pour Vision par ordinateur, vous pouvez générer, ajuster et déployer des modèles d’apprentissage profond pour la classification d’images, la détection d’objets et la similarité d’images.

Essayez les étapes suivantes pour ce package :
1. [Télécharger](https://aka.ms/aml-packages/vision/download) le package.
1. Lire les [instructions d’installation](https://aka.ms/aml-packages/vision).
1. [Créer et déployer un modèle de vision par ordinateur](how-to-build-deploy-image-classification-models.md) à l’aide d’un bloc-notes Jupyter.
1. Explorer la [documentation de référence](https://aka.ms/aml-packages/vision) du package.

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Package ML Azure pour Prévision

Avec le package Azure ML pour Prévision, vous pouvez créer et déployer des modèles de prédiction de série chronologique dans des scénarios de prévision financière et de prévision de la demande.

Essayez les étapes suivantes pour ce package :
1. [Télécharger](https://aka.ms/aml-packages/forecasting/download) le package.
1. Lire les [instructions d’installation](https://aka.ms/aml-packages/forecasting).
1. [Créer et déployer un modèle de prévision](how-to-build-deploy-forecast-models.md) à l’aide d’un bloc-notes Jupyter.
1. Explorer la [documentation de référence](https://aka.ms/aml-packages/forecasting) du package.

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Package Azure ML pour Analyse de texte

Avec le package Azure ML pour Analyse de texte, vous pouvez générer des modèles d’apprentissage profond de texte pour la classification de texte, l’extraction d’entités personnalisées et l’incorporation de mots.

Essayez les étapes suivantes pour ce package :
1. [Télécharger](https://aka.ms/aml-packages/text/download) le package.
1. Lire les [instructions d’installation](https://aka.ms/aml-packages/text).
1. [Créer et déployer un modèle de classification de texte](how-to-build-deploy-text-classification-models.md) à l’aide d’un bloc-notes Jupyter.
1. Explorer la [documentation de référence](https://aka.ms/aml-packages/text) du package.

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (accélération FPGA)

Avec le package de l’accélération matérielle pour Azure Machine Learning, les scientifiques des données et les développeurs IA peuvent caractériser des images avec une version quantifiée de ResNet 50, former des classifieurs en fonction de ces caractéristiques et ensuite déployer des modèles sur un [FPGA (field programmable gate arrays)](concept-accelerate-with-fpgas.md) dans Azure pour une inférence de très faible latence.

Essayez les étapes suivantes pour ce package :
1. [Télécharger](https://aka.ms/aml-real-time-ai-package) le package.
1. Lire les [instructions d’installation](reference-fpga-package-overview.md).
1. [Déployer un modèle sur un FPGA](how-to-deploy-fpga-web-service.md).

