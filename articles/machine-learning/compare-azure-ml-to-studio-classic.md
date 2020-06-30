---
title: Azure Machine Learning et Machine Learning Studio (classique)
description: Quelles sont les différences entre Azure Machine Learning et Machine Learning Studio (classique) ?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 065fd166fb2ce82a3338322d55ef1ee5886ac56b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210369"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning et Machine Learning Studio (classique)

Cet article examine les différences entre Azure Machine Learning et Machine Learning Studio (classique). 

Azure Machine Learning fournit des kits SDK Python et R **et** un concepteur de type « glisser-déposer » pour générer et déployer des modèles Machine Learning. Studio (classique) offre uniquement une expérience de type « glisser-déposer » autonome.

Nous recommandons aux utilisateurs débutants de choisir Azure Machine Learning pour bénéficier de la plus vaste gamme d’outils de pointe dans le domaine du Machine Learning.

## <a name="quick-comparison"></a>Comparaison rapide

Le tableau suivant récapitule quelques-unes des principales différences entre Azure Machine Learning et Studio (classique) :

| | Machine Learning Studio (classique) | Azure Machine Learning |
|---| --- | --- |
| Interface de glisser-déplacer | Prise en charge | Prise en charge - [Concepteur Azure Machine Learning (préversion)](concept-designer.md) <br/>(Nécessite un espace de travail d’entreprise) | 
| Expérience | Évolutive (limite de 10 Go de données de formation) | Mise à l’échelle avec cible de calcul |
| Cibles de calcul d’entraînement | Cible de calcul propriétaire, prise en charge CPU uniquement | Vaste gamme de [cibles de calcul d’entraînement](concept-compute-target.md#train) personnalisables. Processeurs et processeurs graphiques pris en charge | 
| Cibles de calcul de déploiement | Format de service web propriétaire, non personnalisable | Vaste gamme de [cibles de calcul de déploiement](concept-compute-target.md#deploy) personnalisables. Processeurs et processeurs graphiques pris en charge |
| Pipeline ML | Non pris en charge | Création de [pipelines](concept-ml-pipelines.md) modulaires et flexibles pour automatiser les workflows |
| MLOps | Gestion et déploiement des modèles de base | Gestion de versions des entités (modèle, données, workflows), automatisation des workflows, intégration aux outils CICD, [etc.](concept-model-management-and-deployment.md) |
| Format de modèle | Format propriétaire, Studio (classique) uniquement | Plusieurs formats pris en charge selon le type de tâche d’entraînement |
| Entraînement de modèle automatisé et optimisation des hyperparamètres |  Non pris en charge | [Pris en charge dans le SDK et l’espace de travail virtuel](concept-automated-ml.md) | 
| Détection de la dérive de données | Non pris en charge | [Prise en charge dans le SDK et l’espace de travail virtuel](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrer à partir de Machine Learning Studio (classique)

Il n’existe actuellement aucun moyen de migrer des actifs Studio (classique) vers le concepteur Azure Machine Learning (préversion). Les utilisateurs actuels de Studio (classique) peuvent continuer à utiliser leurs actifs de Machine Learning. Toutefois, nous encourageons tous les utilisateurs à utiliser le concepteur, celui-ci offrant une expérience « glisser-déposer » familière, un workflow amélioré **plus** les avantages suivants : scalabilité, gestion de versions et sécurité d’entreprise.

## <a name="get-started-with-azure-machine-learning"></a>Prise en main d’Azure Machine Learning

Les ressources suivantes peuvent vous aider à vous familiariser avec Azure Machine Learning. 

- Lisez la [vue d’ensemble d’Azure Machine Learning](overview-what-is-azure-ml.md).

- Créez votre [première expérience avec le SDK Python](tutorial-1st-experiment-sdk-setup.md).

- [Créez votre premier pipeline de concepteur](tutorial-designer-automobile-price-train-score.md) pour prédire des tarifs automatiques.

![Exemple de concepteur Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Étapes suivantes

En plus des fonctionnalités de glisser-déplacer dans le concepteur, Azure Machine Learning a d’autres outils :  
  + [Utiliser des notebooks Python pour entraîner et déployer des modèles ML](tutorial-1st-experiment-sdk-setup.md)
  + [Utiliser R Markdown pour entraîner et déployer des modèles ML](tutorial-1st-r-experiment.md) 
  + [Utiliser le Machine Learning automatisé pour entraîner et déployer des modèles ML](tutorial-first-experiment-automated-ml.md)  
  + [Utiliser l’interface CLI Machine Learning pour entraîner et déployer un modèle](tutorial-train-deploy-model-cli.md)

