---
title: 'Tutoriel : Créer un modèle prédictif à l’aide du machine learning automatisé (partie 1 sur 2)'
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et déployer des modèles de machine learning automatisé, afin de pouvoir utiliser le meilleur modèle pour prédire les résultats dans Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 4d760f5017c20ac88d3329bb79937c971d4fba75
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459421"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Tutoriel : Intégration de Power BI – Créer un modèle prédictif avec le machine learning automatisé (partie 1 sur 2)

Dans la partie 1 de ce tutoriel, vous allez entraîner et déployer un modèle Machine Learning prédictif. Pour cela, vous allez utiliser le machine learning automatisé dans Azure Machine Learning Studio.  Dans la partie 2, vous utiliserez le modèle le plus performant pour prédire des résultats dans Microsoft Power BI.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un cluster de calcul Azure Machine Learning
> * Créer un jeu de données.
> * Créer une exécution de machine learning automatisé
> * Déployer le meilleur modèle sur un point de terminaison de scoring en temps réel


Il existe trois façons de créer et de déployer le modèle que vous utiliserez dans Power BI.  Cet article concerne l’« Option C : Entraîner et déployer des modèles à l’aide du machine learning automatisé dans Studio ».  Cette option permet une expérience de création sans code. Elle automatise entièrement la préparation des données et l’entraînement du modèle. 

Toutefois, vous pouvez également utiliser l’une des autres options disponibles :

* [Option A : Entraîner et déployer des modèles à l’aide de notebooks Jupyter](tutorial-power-bi-custom-model.md). Cette expérience de création Code First utilise des notebooks Jupyter qui sont hébergés dans Azure Machine Learning Studio.
* [Option B : Entraîner et déployer des modèles à l’aide du concepteur Azure Machine Learning](tutorial-power-bi-designer-model.md). Cette expérience de création avec code minimal utilise une interface utilisateur de type glisser-déposer.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/free/). 
- Un espace de travail Azure Machine Learning. Si vous ne disposez pas encore d’un espace de travail, consultez [Créer et gérer les espaces de travail Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Créer un cluster de calcul

Le machine learning automatisé entraîne automatiquement un grand nombre de modèles Machine Learning pour trouver l’algorithme et les paramètres « les plus adaptés ». Azure Machine Learning parallélise l’exécution de l’entraînement du modèle sur un cluster de calcul.

Pour commencer, dans [Azure Machine Learning Studio](https://ml.azure.com), dans le menu de gauche, sélectionnez **Calcul**. Ouvrez l’onglet **Clusters de calcul**. Sélectionnez ensuite **Nouveau** :

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Capture d’écran montrant comment créer un cluster de calcul.":::

Dans la page **Créer un cluster de calcul** :

1. Sélectionnez une taille de machine virtuelle. Pour ce tutoriel, une machine **Standard_D11_v2** conviendra tout à fait.
1. Sélectionnez **Suivant**.
1. Spécifiez un nom de calcul valide.
1. Maintenez le **nombre minimal de nœuds** à `0`.
1. Définissez le **nombre maximal de nœuds** sur la valeur `4`.
1. Sélectionnez **Create** (Créer).

L’état de votre cluster passe à **Creating** (En cours de création).

>[!NOTE]
> Le nouveau cluster ne comprend aucun nœud. Aucuns frais de calcul ne sont donc générés. Des frais ne sont générés que lorsque vous exécutez des tâches de machine learning automatisé. Le cluster revient automatiquement à 0 après un temps d’inactivité de 120 secondes.


## <a name="create-a-dataset"></a>Créer un jeu de données

Dans ce tutoriel, vous allez utiliser le [jeu de données Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Ce jeu de données est disponible dans [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Pour créer le jeu de données, dans le menu de gauche, sélectionnez **Jeux de données**. Sélectionnez ensuite **Créer un jeu de données**. Vous verrez les options suivantes :

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Capture d’écran montrant comment créer un jeu de données.":::

Sélectionnez **Depuis Open Datasets**. Ensuite, dans la page **Créer un jeu de données à partir d’Open Datasets** :

1. Utilisez la barre de recherche pour rechercher *diabetes*.
1. Sélectionnez **Exemple : Diabetes**.
1. Sélectionnez **Suivant**.
1. Nommez votre jeu de données *diabetes*.
1. Sélectionnez **Create** (Créer).

Pour explorer les données, sélectionnez le jeu de données, puis sélectionnez **Explorer** :

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Capture d’écran montrant comment explorer un jeu de données.":::

Les données contiennent 10 variables d’entrée de référence (telles que l’âge, le sexe, l’indice de masse corporelle, la pression artérielle moyenne et six dosages de plasma sanguin). Elles contiennent également une variable cible nommée **Y**. Cette variable cible comprend une mesure quantitative de la progression du diabète, effectuée un an après la base de référence.

## <a name="create-an-automated-machine-learning-run"></a>Créer une exécution de machine learning automatisé

Dans [Azure Machine Learning Studio](https://ml.azure.com), dans le menu de gauche, sélectionnez **ML automatisé**. Sélectionnez ensuite **Nouvelle exécution de ML automatisé** :

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Capture d’écran montrant comment créer une nouvelle exécution de machine learning automatisé.":::

Ensuite, sélectionnez le jeu données **diabetes** que vous avez créé précédemment. Ensuite, sélectionnez **Suivant** :

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Capture d’écran montrant comment sélectionner un jeu données.":::
 
Dans la page **Configurer l’exécution** :

1. Sous **Nom de l’expérience**, sélectionnez **Créer nouveau**.
1. Attribuez un nom à l’expérience.
1. Dans le champ **Colonne cible**, sélectionnez **Y**.
1. Dans le champ **Sélectionner un cluster de calcul**, sélectionnez le cluster de calcul que vous avez créé précédemment. 

Votre formulaire rempli doit ressembler à ceci :

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Capture d’écran montrant comment configurer le machine learning automatisé.":::

Enfin, sélectionnez une tâche de machine learning. Dans ce cas, la tâche est **Régression** :

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Capture d’écran montrant comment configurer une tâche.":::

Sélectionnez **Terminer**.

> [!IMPORTANT]
> Le machine learning automatisé a besoin d’environ 30 minutes pour effectuer l’entraînement des 100 modèles.

## <a name="deploy-the-best-model"></a>Déployer le meilleur modèle

Une fois que l’exécution du machine learning automatisé est terminée, vous pouvez afficher la liste de tous les modèles Machine Learning qui ont été essayés en sélectionnant l’onglet **Modèles**. Les modèles sont classés dans l’ordre de leurs performances : le modèle le plus performant figure en tête de liste. Lorsque vous sélectionnez le meilleur modèle, le bouton **Déployer** est activé :

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Capture d’écran montrant la liste des modèles.":::

Sélectionnez **Déployer** pour ouvrir une fenêtre **Déployer un modèle** :

1. Nommez votre service de modèle *diabetes-model*.
1. Sélectionnez **Azure Container Service**.
1. Sélectionnez **Déployer**.

Un message indiquant que le modèle a été correctement déployé doit s’afficher.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez vu comment entraîner et déployer un modèle Machine Learning à l’aide du machine learning automatisé. Le tutoriel suivant explique comment consommer ce modèle (lui attribuer un score) dans Power BI.

> [!div class="nextstepaction"]
> [Tutoriel : Consommer un modèle dans Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
