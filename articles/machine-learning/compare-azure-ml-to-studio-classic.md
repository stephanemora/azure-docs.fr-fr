---
title: Azure Machine Learning et Machine Learning Studio (classique)
description: Différences entre Azure Machine Learning et Machine Learning Studio (classique)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974131"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Différences entre Azure Machine Learning et Machine Learning Studio (classique)

Cet article compare les caractéristiques, les fonctionnalités et l’interface d’Azure Machine Learning à Machine Learning Studio (classique). 

## <a name="about-machine-learning-studio-classic"></a>À propos de Machine Learning Studio (classique)
[Machine Learning Studio (classique)](studio/what-is-ml-studio.md) est un espace de travail visuel collaboratif par glisser-déplacer dans lequel vous pouvez générer, tester et déployer des solutions de machine learning sans avoir à écrire du code. Il s’appuie sur des algorithmes de machine learning et des modules de traitement des données prédéfinis et préconfigurés, mais aussi sur une plateforme de calcul propriétaire.

## <a name="about-azure-machine-learning"></a>À propos d'Azure Machine Learning

[Azure Machine Learning](overview-what-is-azure-ml.md) propose également une interface web appelée le concepteur (préversion) **et** plusieurs Kits de développement logiciel (SDK) et interface CLI pour préparer rapidement les données, entraîner et déployer des modèles Machine Learning. Avec Azure Machine Learning, vous bénéficiez d’une mise à l’échelle, d’une prise en charge de plusieurs infrastructures, de fonctionnalités de ML avancées, telles que le Machine Learning automatisé et la prise en charge de pipeline.

Le concepteur Azure Machine Learning offre une expérience de glisser-déplacer similaire à Studio (classique). Cependant, contrairement à la plateforme de calcul propriétaire de Studio (classique), le concepteur utilise vos propres ressources de calcul, est évolutif, et est entièrement intégré à Azure Machine Learning.  

> [!TIP]
> Les clients qui utilisent ou évaluent actuellement Machine Learning Studio (classique) sont encouragés à essayer le [concepteur Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer) (préversion), qui fournit des modules ML par glisser-déposer __plus__ l’extensibilité, la gestion de versions et la sécurité de l’entreprise.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Comparaison : Azure Machine Learning et Machine Learning Studio (classique)

Voici une comparaison rapide.

||  Concepteur Azure Machine Learning|Studio (classique) |
|---| --- | --- |
||Le concepteur est en préversion, Azure Machine Learning est en disponibilité générale|Disponibilité générale (GA) | 
|Interface de glisser-déposer| Oui | Oui|
|Expérience| Mise à l’échelle avec cible de calcul|Mise à l’échelle (limite de données d’entraînement de 10 Go) | 
|Modules pour l’interface| [Nombreux modules populaires](algorithm-module-reference/module-reference.md) | Divers |
|Cibles de calcul d’entraînement| Calcul AML (GPU/processeur)|Cible de calcul propriétaire, processeur uniquement|
|Inférence des cibles de calcul| Azure Kubernetes Service pour l’inférence en temps réel <br/>Calcul AML pour l’inférence par lot|Format de service web propriétaire, non personnalisable | 
|Pipeline ML| Création de pipeline <br/> Pipeline publié <br/> Point de terminaison de pipeline <br/> [En savoir plus sur le pipeline ML](concept-ml-pipelines.md)|Non pris en charge | 
|Opérations ML| Déploiement configurable, gestion de versions des modèles et pipelines|Gestion et déploiement des modèles de base | 
|Modèle| Format standard, différent, dépend du travail d’entraînement|Format propriétaire non portable.| 
|Apprentissage automatisé des modèles|Pas encore dans le concepteur, mais possible via l’interface et les Kits de développement logiciel (SDK).| Non | 

## <a name="get-started-with-azure-machine-learning"></a>Prise en main d’Azure Machine Learning

Les ressources suivantes peuvent vous aider dans la prise en main d’Azure Machine Learning

- Lire la [Vue d’ensemble d’Azure Machine Learning](tutorial-first-experiment-automated-ml.md) 

- [Créez votre premier pipeline de concepteur](tutorial-designer-automobile-price-train-score.md) pour prédire des tarifs automatiques.

![Exemple de concepteur Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Étapes suivantes

En plus des fonctionnalités de glisser-déplacer dans le concepteur, Azure Machine Learning a d’autres outils :  
  + [Utiliser des notebooks Python pour entraîner et déployer des modèles ML](tutorial-1st-experiment-sdk-setup.md)
  + [Utiliser R Markdown pour entraîner et déployer des modèles ML](tutorial-1st-r-experiment.md) 
  + [Utiliser le Machine Learning automatisé pour entraîner et déployer des modèles ML](tutorial-designer-automobile-price-train-score.md) 
  + [Utiliser l’interface CLI Machine Learning pour entraîner et déployer un modèle](tutorial-train-deploy-model-cli.md)

