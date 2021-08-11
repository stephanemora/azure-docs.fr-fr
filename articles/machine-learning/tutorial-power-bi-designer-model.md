---
title: 'Tutoriel : Créer un modèle prédictif par glisser-déposer (partie 1 sur 2)'
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et déployer un modèle Machine Learning prédictif à l’aide du concepteur. Vous pourrez l’utiliser plus tard pour prédire des résultats dans Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 9390fc53f4d10ad770313f1f1459f02fc7e7a998
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459439"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Tutoriel : Intégration Power BI – Créer un modèle prédictif par glisser-déposer (partie 1 sur 2)

Dans la partie 1 de ce tutoriel, vous allez entraîner et déployer un modèle Machine Learning prédictif en utilisant le concepteur Azure Machine Learning. Ce concepteur est constitué d’une interface utilisateur de type « glisser-déposer » avec code minimal. Dans la partie 2, vous utiliserez le modèle pour prédire des résultats dans Microsoft Power BI.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer une instance de calcul Azure Machine Learning
> * Créer un cluster d’inférence Azure Machine Learning
> * Créer un jeu de données.
> * Entraîner un modèle de régression
> * Déployer le modèle sur un point de terminaison de scoring en temps réel


Il existe trois façons de créer et de déployer le modèle que vous utiliserez dans Power BI.  Cet article concerne l’« Option B : Entraîner et déployer des modèles à l’aide du concepteur ».  Cette option propose une expérience de création avec code minimal qui utilise l’interface du concepteur.  

Toutefois, vous pouvez également utiliser l’une des autres options disponibles :

* [Option A : Entraîner et déployer des modèles à l’aide de notebooks Jupyter](tutorial-power-bi-custom-model.md). Cette expérience de création Code First utilise des notebooks Jupyter qui sont hébergés dans Azure Machine Learning Studio.
* [Option C : Entraîner et déployer des modèles à l’aide du machine learning automatisé](tutorial-power-bi-automated-model.md). Cette expérience de création sans code automatise entièrement la préparation des données et l’entraînement du modèle.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/free/). 
- Un espace de travail Azure Machine Learning. Si vous ne disposez pas encore d’un espace de travail, consultez [Créer et gérer les espaces de travail Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Connaissances préliminaires des workflows de Machine Learning.


## <a name="create-compute-to-train-and-score"></a>Créer une instance de calcul pour entraîner et attribuer un score

Dans cette section, vous allez créer une *instance de calcul*. Les instances de calcul sont utilisées pour l’entraînement des modèles Machine Learning. Vous allez également créer un *cluster d’inférence* afin d’héberger le modèle déployé pour le scoring en temps réel.

Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com). Dans le menu de gauche, sélectionnez **Calcul**, puis **Nouveau** :

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Capture d’écran montrant comment créer une instance de calcul.":::

Dans la page **Créer une instance de calcul**, sélectionnez une taille de machine virtuelle. Pour ce tutoriel, sélectionnez une machine virtuelle **Standard_D11_v2**. Sélectionnez ensuite **Suivant**. 

Dans la page **Paramètres**, attribuez un nom à votre instance de calcul. Sélectionnez ensuite **Créer**. 

>[!TIP]
> Vous pouvez également utiliser l’instance de calcul pour créer et exécuter des notebooks.

L’**état** de votre instance de calcul est passé à **Creating** (En cours de création). Le provisionnement de la machine prend environ 4 minutes. 

Pendant le provisionnement, dans la page **Calcul**, sélectionnez l’onglet **Clusters d’inférence**. Sélectionnez ensuite **Nouveau** :

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Capture d’écran montrant comment créer un cluster d’inférence.":::

Dans la page **Créer un cluster d’inférence**, sélectionnez une région et une taille de machine virtuelle. Pour ce tutoriel, sélectionnez une machine virtuelle **Standard_D11_v2**. Sélectionnez ensuite **Suivant**. 

Dans la page **Configurer les paramètres** :

1. Spécifiez un nom de calcul valide.
1. Sélectionnez **Dev-test** comme rôle de cluster. Cette option crée un nœud unique dans lequel héberger le modèle déployé.
1. Sélectionnez **Create** (Créer).

L’**état** de votre cluster d’inférence est passé à **Creating** (En cours de création). Le déploiement de votre cluster à nœud unique prendra environ 4 minutes.

## <a name="create-a-dataset"></a>Créer un jeu de données

Dans ce tutoriel, vous allez utiliser le [jeu de données Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Ce jeu de données est disponible dans [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Pour créer le jeu de données, dans le menu de gauche, sélectionnez **Jeux de données**. Sélectionnez ensuite **Créer un jeu de données**. Vous verrez les options suivantes :

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Capture d’écran montrant comment créer un jeu de données.":::

Sélectionnez **Depuis Open Datasets**. Dans la page **Créer un jeu de données à partir d’Open Datasets** :

1. Utilisez la barre de recherche pour rechercher *diabetes*.
1. Sélectionnez **Exemple : Diabetes**.
1. Sélectionnez **Suivant**.
1. Nommez votre jeu de données *diabetes*.
1. Sélectionnez **Create** (Créer).

Pour explorer les données, sélectionnez le jeu de données, puis sélectionnez **Explorer** :

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Capture d’écran montrant comment explorer un jeu de données.":::

Les données contiennent 10 variables d’entrée de référence (telles que l’âge, le sexe, l’indice de masse corporelle, la pression artérielle moyenne et six dosages de plasma sanguin). Elles contiennent également une variable cible nommée **Y**. Cette variable cible comprend une mesure quantitative de la progression du diabète, effectuée un an après la base de référence.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Créer un modèle Machine Learning à l’aide du concepteur

Après avoir créé l’instance de calcul et les jeux de données, vous pouvez utiliser le concepteur pour créer le modèle Machine Learning. Dans Azure Machine Learning Studio, sélectionnez **Concepteur**, puis **Nouveau pipeline** :

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Capture d’écran montrant comment créer un pipeline.":::

Vous voyez un *canevas* vide et le menu **Paramètres** :

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Capture d’écran montrant comment sélectionner une cible de calcul.":::

Dans le menu **Paramètres**, choisissez **Sélectionner une cible de calcul**. Sélectionnez l’instance de calcul que vous avez créée précédemment, puis sélectionnez **Enregistrer**. Remplacez le **nom Brouillon** par un nom plus facile à mémoriser, comme *diabetes-model*. Enfin, entrez une description.

Dans la liste des ressources, développez **Jeux de données** et recherchez le jeu de données **diabetes**. Faites glisser ce composant sur le canevas :

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Capture d’écran montrant comment faire glisser un composant vers le canevas.":::

Ensuite, faites glisser les composants suivants sur le canevas :

1. **Régression linéaire** (situé dans **Algorithmes de Machine Learning**)
1. **Entraîner le modèle** (situé dans **Entraînement de modèle**)

Sur votre canevas, remarquez les cercles situés en haut et en bas des composants. Ces cercles sont des ports.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Capture d’écran montrant les ports de composants non connectés":::
 
À présent, *connectez* les composants entre eux. Sélectionnez le port au bas du jeu de données **diabetes**. Faites-le glisser vers le port situé en haut à droite du composant **Entraîner le modèle**. Sélectionnez le port au bas du composant **Régression linéaire**. Faites-le glisser vers le port situé en haut à gauche du composant **Entraîner le modèle**.

Choisissez la colonne du jeu de données à utiliser comme la variable d’étiquette (cible) à prédire. Sélectionnez le composant **Entraîner le modèle**, puis **Modifier la colonne**. 

Dans la boîte de dialogue, sélectionnez **Entrez le nom de la colonne** > **Y** :

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Capture d’écran montrant comment sélectionner une colonne d’étiquette.":::

Sélectionnez **Enregistrer**. Votre *workflow* de Machine Learning doit se présenter de la façon suivante :

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Capture d’écran montrant des composants connectés.":::

Sélectionnez **Envoyer**. Sous **Expérience**, sélectionnez **Créer nouveau**. Attribuez un nom à l’expérience, puis sélectionnez **Envoyer**.

>[!NOTE]
> La première exécution de votre expérience prendra environ 5 minutes. Les exécutions suivantes seront beaucoup plus rapides, car le concepteur met en cache les composants qui ont déjà été exécutés pour réduire la latence.

Une fois l’exécution de l’expérience terminée, vous verrez ceci :

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Capture d’écran montrant l’exécution terminée.":::

Pour inspecter les journaux des expériences, sélectionnez **Entraîner le modèle**, puis **Sorties + journaux**.

## <a name="deploy-the-model"></a>Déployer le modèle

Pour déployer le modèle, en haut du canevas, sélectionnez **Créer un pipeline d’inférence** > **Pipeline d’inférence en temps réel** :

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Capture d’écran montrant comment sélectionner un pipeline d’inférence en temps réel.":::
 
Le pipeline se réduit uniquement aux composants nécessaires au scoring du modèle. Lorsque vous attribuez un score aux données, vous ne connaissez pas les valeurs des variables cibles. Vous pouvez donc supprimer **Y** du jeu de données. 

Pour supprimer **Y**, ajoutez au canevas un composant **Sélectionner des colonnes dans le jeu de données**. Connectez le composant de sorte que le jeu de données « diabetes » devienne l’entrée. Les résultats correspondent à la sortie du composant **Effectuer le scoring d’un modèle** :

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Capture d’écran montrant comment supprimer une colonne.":::

Dans le canevas, sélectionnez le composant **Sélectionner des colonnes dans le jeu de données**, puis **Modifier les colonnes**. 

Dans la boîte de dialogue **Sélectionner des colonnes**, choisissez **Par nom**. Vérifiez ensuite que toutes les variables d’entrée sont sélectionnées, mais que la cible *n’est pas* sélectionnée :

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Capture d’écran montrant comment supprimer les paramètres d’une colonne.":::

Sélectionnez **Enregistrer**. 

Enfin, sélectionnez le composant **Effectuer le scoring d’un modèle** et vérifiez que la case **Ajouter les colonnes de scoring à la sortie** n’est pas cochée. Pour réduire la latence, les prédictions sont retournées sans les entrées.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Capture d’écran montrant les paramètres du composant Effectuer le scoring d’un modèle.":::

En haut du canevas, sélectionnez **Envoyer**.

Une fois que vous avez correctement exécuté le pipeline d’inférence, vous pouvez déployer le modèle dans votre cluster d’inférence. Sélectionnez **Déployer**. 

Dans la boîte de dialogue **Configurer le point de terminaison en temps réel**, sélectionnez **Déployer un nouveau point de terminaison en temps réel**. Attribuez un nom au point de terminaison *my-diabetes-model*. Sélectionnez l’inférence que vous avez créée précédemment, puis sélectionnez **Déployer** :

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Capture d’écran montrant les paramètres de point de terminaison en temps réel.":::
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez vu comment entraîner et déployer un modèle de concepteur. Dans la partie suivante, vous allez voir comment consommer ce modèle (lui attribuer un score) dans Power BI.

> [!div class="nextstepaction"]
> [Tutoriel : Consommer un modèle dans Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
