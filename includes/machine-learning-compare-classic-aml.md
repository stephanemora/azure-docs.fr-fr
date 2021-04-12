---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563185"
---
Le tableau suivant récapitule les principales différences entre ML Studio (classique) et Azure Machine Learning.

| Fonctionnalité | ML Studio (classique) | Azure Machine Learning |
|---| --- | --- |
| Interface de glisser-déplacer | Expérience classique | Expérience mise à jour : [concepteur Azure Machine Learning](../articles/machine-learning/concept-designer.md)| 
| SDK de code | Non pris en charge | Intégration totale aux SDK [R](/python/api/overview/azure/ml/) et [Python pour Azure Machine Learning](https://github.com/Azure/azureml-sdk-for-r) |
| Expérience | Évolutive (limite de 10 Go de données de formation) | Mise à l’échelle avec cible de calcul |
| Cibles de calcul d’entraînement | Cible de calcul propriétaire, prise en charge CPU uniquement | Vaste gamme de [cibles de calcul d’entraînement](../articles/machine-learning/concept-compute-target.md#train) personnalisables. Processeurs et processeurs graphiques pris en charge | 
| Cibles de calcul de déploiement | Format de service web propriétaire, non personnalisable | Vaste gamme de [cibles de calcul de déploiement](../articles/machine-learning/concept-compute-target.md#deploy) personnalisables. Processeurs et processeurs graphiques pris en charge |
| Pipeline ML | Non pris en charge | Création de [pipelines](../articles/machine-learning/concept-ml-pipelines.md) modulaires et flexibles pour automatiser les workflows |
| MLOps | Gestion et déploiement des modèles de base ; déploiement de processeur uniquement | Gestion de versions des entités (modèle, données, workflows), automatisation des workflows, intégration aux outils CI/CD, déploiements de processeur et de GPU, [etc.](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Format de modèle | Format propriétaire, Studio (classique) uniquement | Plusieurs formats pris en charge selon le type de tâche d’entraînement |
| Entraînement de modèle automatisé et optimisation des hyperparamètres |  Non pris en charge | [Pris en charge](../articles/machine-learning/concept-automated-ml.md). Options Code First et sans code. | 
| Détection de la dérive de données | Non pris en charge | [Pris en charge](../articles/machine-learning/how-to-monitor-datasets.md) |
| Projets d'étiquetage de données | Non pris en charge | [Pris en charge](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Contrôle d’accès en fonction du rôle | Rôle de contributeur et de propriétaire uniquement | [Définition de rôle flexible et contrôle RBAC](../articles/machine-learning/how-to-assign-roles.md) |
| Galerie IA | Pris en charge ([https://gallery.azure.ai/](https://gallery.azure.ai/)) | Non pris en charge <br><br> Apprendre avec les [exemples de notebooks du kit SDK Python](https://github.com/Azure/MachineLearningNotebooks). |