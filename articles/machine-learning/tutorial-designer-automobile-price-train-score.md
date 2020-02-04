---
title: 'Tutoriel : Prédire le prix de voitures avec le concepteur'
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner, scorer et déployer un modèle Machine Learning via une interface de type glisser-déposer. Ce tutoriel constitue la première partie d’une série en deux volets sur la prédiction des prix des véhicules automobiles à l’aide d’une régression linéaire.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 639a61cddde27b0d989e5a3dd4c599c353182a73
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720162"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Tutoriel : Prédire le prix de voitures avec le concepteur (version préliminaire)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dans ce tutoriel en deux parties, vous découvrez comment utiliser le concepteur d’Azure Machine Learning pour développer et déployer une solution d’analytique prédictive qui prédit le prix de n’importe quel véhicule.

Dans la première partie du tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un pipeline
> * Importer des données
> * Préparer les données
> * Entraîner un modèle Machine Learning
> * Évaluer un modèle Machine Learning

Dans la [deuxième partie](tutorial-designer-automobile-price-deploy.md) du tutoriel, vous allez déployer votre modèle en tant que point de terminaison d’inférence en temps réel pour prédire le prix de n’importe quel véhicule en fonction des caractéristiques techniques que vous lui envoyez. 

> [!NOTE]
>Une version complète de ce tutoriel est disponible en tant qu’exemple de pipeline.
>
>Pour le trouver, accédez au concepteur dans votre espace de travail. Dans la section **Nouveau pipeline**, sélectionnez **Exemple 1 - Régression : Prédiction du prix de véhicules automobiles (de base)** .

## <a name="create-a-new-pipeline"></a>Créer un pipeline

Les pipelines Azure Machine Learning organisent plusieurs étapes de machine learning et de traitement de données en une même ressource. Les pipelines vous permettent d’organiser, de gérer et de réutiliser des workflows de Machine Learning complexes entre des projets et des utilisateurs.

Pour créer un pipeline Azure Machine Learning, vous devez disposer d’un espace de travail Azure Machine Learning. Dans cette section, vous découvrez comment créer ces deux ressources.

### <a name="create-a-new-workspace"></a>Créer un espace de travail

Si vous avez un espace de travail Azure Machine Learning édition Entreprise, [passez à la section suivante](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Créer le pipeline

1. Connectez-vous à [ml.azure.com](https://ml.azure.com), puis sélectionnez l’espace de travail à utiliser.

1. Sélectionnez **Concepteur**.

    ![Capture d’écran de l’espace de travail visuel montrant comment accéder au concepteur](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Sélectionnez **Modules prédéfinis faciles à utiliser**.

1. En haut du canevas, sélectionnez le nom de pipeline par défaut **Pipeline-Created-on**. Renommez-le *Automobile price prediction*. Le nom n’a pas besoin d’être unique.

## <a name="import-data"></a>Importer des données

Un certain nombre d’exemples de jeux de données que vous pouvez expérimenter sont inclus dans le concepteur. Pour les besoins de ce tutoriel, vous allez utiliser **Automobile price data (Raw)** (Données sur le prix des véhicules automobiles [brutes]). 

1. Sur la gauche du canevas de pipeline se trouve une palette de jeux de données et de modules. Sélectionnez **Datasets** (Jeux de données), puis affichez la section **Samples** (Exemples) pour voir les exemples de jeux de données disponibles.

1. Sélectionnez le jeu de données **Automobile price data (raw)** (Données sur le prix des automobiles (brut)), puis faites-le glisser vers le canevas.

   ![Faites glisser les données jusqu’au canevas](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualiser les données

Vous pouvez visualiser les données pour comprendre le jeu de données que vous allez utiliser.

1. Sélectionnez le module **Automobile price data (Raw)** .

1. Dans le volet de propriétés à droite du canevas, sélectionnez **Outputs** (Sorties).

1. Sélectionnez l’icône de graphique pour visualiser les données.

    ![Visualiser les données](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Cliquez sur différentes colonnes dans la fenêtre de données pour visualiser des informations les concernant.

    Chaque ligne représente un véhicule automobile et chaque colonne représente une variable associée au véhicule automobile. Ce jeu de données contient 205 lignes et 26 colonnes.

## <a name="prepare-data"></a>Préparer les données

Les jeux de données nécessitent généralement un prétraitement avant l’analyse. Vous avez peut-être remarqué qu’il manquait des valeurs durant l’inspection du jeu de données. Ces valeurs manquantes doivent être nettoyées pour que le modèle puisse analyser les données correctement.

### <a name="remove-a-column"></a>Supprimer une colonne

Quand vous entraînez un modèle, vous devez traiter le problème des données manquantes. Dans ce jeu de données, il manque beaucoup de valeurs dans la colonne **normalized-losses** (pertes normalisées). Vous excluez donc cette colonne du modèle.

1. Dans la zone de recherche située en haut de la palette, entrez **Select** pour rechercher le module **Select Columns in Dataset** (Sélectionner des colonnes dans le jeu de données).

1. Faites glisser le module **Select Columns in Dataset** vers le canevas. Déposez-le sous le module de jeu de données.

1. Connectez le jeu de données **Automobile price data (Raw)** (Données sur le prix des automobiles (brut)) au module **Select Columns in Dataset**. Faites-le glisser depuis le port de sortie du jeu de données, qui est le petit cercle situé en bas du jeu de données sur le canevas, jusqu’au port d’entrée de **Select Columns in Dataset**, qui est le petit cercle en haut du module.

    > [!TIP]
    > Vous créez un flux de données à travers votre pipeline quand vous connectez le port de sortie d’un module au port d’entrée d’un autre module.
    >

    ![Connecter des modules](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Sélectionnez le module **Select Columns in Dataset**.

1. Dans le volet de propriétés à droite du canevas, sélectionnez **Toutes les colonnes**.

1. Sélectionnez le signe **+** pour ajouter une règle.

1. Dans le menu déroulant, sélectionnez **Exclure** et **Noms des colonnes**.
    
1. Entrez *normalized-losses* (pertes normalisées) dans la zone de texte.

1. En bas à droite, sélectionnez **Enregistrer** pour fermer le sélecteur de colonne.

    ![Exclure une colonne](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Sélectionnez le module **Select Columns in Dataset**. 

1. Dans le volet de propriétés, sélectionnez la zone de texte **Comment** (Commentaire) et entrez *Exclude normalized losses* (Exclure les pertes normalisées).

    Les commentaires sont affichés sur le graphe pour vous aider à organiser votre pipeline.

### <a name="clean-missing-data"></a>Nettoyage des données manquantes

Il manque encore des valeurs dans votre jeu de données après la suppression de la colonne **normalized-losses**. Vous pouvez supprimer les données manquantes restantes à l’aide du module **Clean Missing Data** (Nettoyer les données manquantes).

> [!TIP]
> Le nettoyage des valeurs manquantes dans les données d’entrée est un prérequis pour l’utilisation de la plupart des modules dans le concepteur.

1. Entrez **Clean** dans la zone de recherche pour rechercher le module **Clean Missing Data**.

1. Faites glisser le module **Clean Missing Data** vers le canevas du pipeline. Connectez-le au module **Select Columns in Dataset**. 

1. Dans le volet de propriétés, sélectionnez **Remove entire row** (Supprimer la ligne entière) sous **Cleaning mode** (Mode de nettoyage).

1. Dans la zone **Comment** du volet de propriétés, entrez *Remove missing value rows* (Supprimer les lignes avec des valeurs manquantes). 

    Votre pipeline doit maintenant se présenter comme ceci :
    
    ![Sélectionner une colonne](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Entraîner un modèle Machine Learning

Une fois les modules en place pour traiter les données, vous pouvez configurer les modules d’entraînement.

Comme vous voulez prédire un prix, à savoir un nombre, vous pouvez utiliser un algorithme de régression. Pour cet exemple, vous utilisez un modèle de régression linéaire.

### <a name="split-the-data"></a>Fractionner les données

Le fractionnement des données est une tâche courante de Machine Learning. Vous allez diviser vos données en deux jeux de données distincts. Un jeu de données entraîne le modèle et l’autre teste les performances du modèle.

1. Entrez **split data** (diviser les données) dans la zone de recherche pour trouver le module **Split Data** (Diviser les données). Connectez le port de gauche du module **Clean Missing Data** au module **Split Data**.

    > [!IMPORTANT]
    > Vérifiez que le port de sortie de gauche de **Clean Missing Data** est connecté à **Split Data**. Le port de gauche contient les données nettoyées. Le port de droite contient les données ignorées.

1. Sélectionnez le module **Fractionner les données**.

1. Dans le volet de propriétés, affectez à l’option **Fraction of rows in the first output dataset** (Fraction de lignes du premier jeu de données de sortie) la valeur 0.7 (0,7).

    Cette option permet de diviser les données afin d’en utiliser 70 % pour entraîner le modèle et 30 % pour tester ce dernier. Le jeu de données comprenant 70 % des données est accessible par le biais du port de sortie de gauche. Les données restantes sont disponibles par le biais du port de sortie de droite.

1. Dans la zone **Comment** du volet de propriétés, entrez *Diviser le jeu de données en un jeu d’entraînement (0,7) et un jeu de test (0,3)* .

### <a name="train-the-model"></a>Effectuer l’apprentissage du modèle

Entraînez le modèle en lui fournissant un jeu de données incluant le prix. L’algorithme construit un modèle qui explique la relation entre les caractéristiques et le prix dans les données d’entraînement.

1. Pour sélectionner l’algorithme d’apprentissage, effacez le contenu de la zone de recherche de votre palette de modules.

1. Développez **Algorithmes de Machine Learning**.
    
    Cette option affiche plusieurs catégories de module qui vous permettent d’initialiser les algorithmes d’apprentissage.

1. Sélectionnez **Regression** > **Linear Regression** (Régression > Régression linéaire), puis faites glisser le module vers le canevas du pipeline.

1. Recherchez et faites glisser le module **Entraîner le modèle** jusqu’au canevas du pipeline. 

1. Connectez la sortie du module **Linear Regression** (Régression linéaire) à l’entrée gauche du module **Train model** (Entraîner le modèle).

1. Connectez la sortie des données d’entraînement (port de gauche) du module **Fractionner les données** à l’entrée droite du module **Entraîner le modèle**.
    
    > [!IMPORTANT]
    > Vérifiez que le port de sortie de gauche de **Split Data** est connecté à **Train Model**. Le port de gauche contient le jeu d’entraînement. Le port de droite contient le jeu de test.

    ![Capture d’écran montrant la configuration correcte du module Entraîner le modèle Le module Régression linéaire se connecte au port gauche du module Entraîner le modèle et le module Fractionner les données se connecte au port droit du module Entraîner le modèle.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Sélectionnez le module **Entraîner le modèle**.

1. Dans le volet de propriétés, sélectionnez le sélecteur **Edit column** (Modifier la colonne).

1. Dans la boîte de dialogue **Label column** (Étiqueter une colonne), développez le menu déroulant, puis sélectionnez **Column names** (Noms de colonnes). 

1. Dans la zone de texte, entrez *price* pour spécifier la valeur que votre modèle va prédire.

    Votre pipeline doit se présenter comme suit :

    ![Capture d’écran montrant la configuration correcte du pipeline après l’ajout du module Entraîner le modèle.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="score-a-machine-learning-model"></a>Attribuer un score à un modèle Machine Learning

Une fois que vous avez entraîné votre modèle à l’aide de 70 % des données, vous pouvez l’utiliser pour attribuer un score aux 30 % de données restants, et vérifier ainsi son bon fonctionnement.

1. Entrez *score model* (score du modèle) dans la zone de recherche pour trouver le module **Score Model**. Faites glisser le module vers le canevas du pipeline. 

1. Connectez la sortie du module **Entraîner le modèle** au port d’entrée de gauche du module **Scorer le modèle**. Connectez la sortie des données de test (port de droite) du module **Fractionner les données** au port d’entrée de droite du module **Scorer le modèle**.

## <a name="evaluate-a-machine-learning-model"></a>Évaluer un modèle Machine Learning

Utilisez le module **Evaluate Model** (Évaluer le modèle) pour évaluer le score attribué par votre modèle au jeu de données de test.

1. Entrez *evaluate* (évaluer) dans la zone de recherche pour trouver le module **Evaluate Model** (Évaluer le modèle). Faites glisser le module vers le canevas du pipeline. 

1. Connectez la sortie du module **Scorer le modèle** à l’entrée de gauche du module **Évaluer le modèle**. 

    Le pipeline final doit maintenant se présenter comme ceci :

    ![Capture d’écran montrant la configuration correcte du pipeline](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>Exécuter le pipeline

[!INCLUDE [aml-ui-create-training-compute](../../includes/aml-ui-create-training-compute.md)]

### <a name="view-scored-labels"></a>Afficher les étiquettes de score

Une fois l’exécution terminée, vous pouvez voir les résultats de l’exécution du pipeline. Tout d’abord, examinez les prédictions générées par le modèle de régression.

1. Sélectionnez le module **Score Model** pour afficher sa sortie.

1. Dans le volet de propriétés, sélectionnez **Sorties** > icône de graphe ![icône de visualisation](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) pour voir les résultats.

    Vous pouvez voir ici les prix prédits et les prix réels des données à partir des données de test.

    ![Capture d’écran de la visualisation de la sortie mettant en évidence la colonne Scored Labels (Étiquettes de score)](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Évaluer les modèles

Utilisez **Evaluate Model** pour voir ce que donne le modèle entraîné sur le jeu de données de test.

1. Sélectionnez le module **Evaluate Model** pour afficher sa sortie.

1. Dans le volet de propriétés, sélectionnez **Sortie** > icône de graphe ![icône de visualisation](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) pour voir les résultats.

Les statistiques suivantes s’affichent pour votre modèle :

* **Erreur absolue moyenne** : Moyenne des erreurs absolues. Une erreur est la différence entre la valeur prédite et la valeur réelle.
* **Racine carrée de l’erreur quadratique moyenne** : la racine carrée de la moyenne des erreurs carrées des prévisions effectuées sur le jeu de données de test.
* **Erreur absolue relative**: la moyenne des erreurs absolues relative à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Erreur carrée relative** : la moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Coefficient de détermination** : Également connue sous le nom de valeur R au carré, cette métrique statistique indique dans quelle mesure un modèle correspond aux données.

Pour chacune des statistiques liées aux erreurs, les valeurs les plus petites sont privilégiées. En effet, une valeur plus petite indique que les prédictions sont plus près des valeurs réelles. Plus la valeur du coefficient de détermination est proche de un (1,0), plus les prévisions sont correctes.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cette première partie du tutoriel, vous avez effectué les tâches suivantes :

* Créer un pipeline
* Préparer les données
* Effectuer l’apprentissage du modèle
* Scorer et évaluer le modèle

Dans la deuxième partie, vous allez découvrir comment déployer votre modèle en tant que point de terminaison en temps réel.

> [!div class="nextstepaction"]
> [Continuer avec le déploiement des modèles](tutorial-designer-automobile-price-deploy.md)
