---
title: 'Didacticiel : Prédire le prix de véhicules automobiles à l’aide de l’interface visuelle'
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner, évaluer et déployer un modèle Machine Learning à l’aide d’une interface visuelle de type glisser-déplacer. Ce tutoriel constitue la première partie d’une série en deux volets sur la prédiction des prix de véhicules automobiles au moyen d’une régression linéaire.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 3852531615418ffe5397295bc194de34139d6e81
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792643"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Didacticiel : Prédire le prix de véhicules automobiles à l’aide de l’interface visuelle

Dans ce tutoriel en deux parties, vous découvrez comment utiliser l’interface visuelle d’Azure Machine Learning pour développer et déployer une solution d’analytique prédictive qui prédit le prix de n’importe quel véhicule. 

Dans la première partie, vous configurez votre environnement, vous faites un glisser-déplacer des jeux de données et des modules d’analyse sur un canevas interactif, puis vous les connectez ensemble pour créer un pipeline Azure Machine Learning.

Dans la première partie du tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un pipeline
> * Importer des données
> * Préparer les données
> * Entraîner un modèle Machine Learning
> * Évaluer un modèle Machine Learning

Dans la [deuxième partie](ui-tutorial-automobile-price-deploy.md) du tutoriel, vous découvrez comment déployer votre modèle prédictif en tant que service web Azure, pour prédire le prix de n’importe quel véhicule en fonction des caractéristiques techniques que vous lui envoyez. 

> [!Note]
>Une version complète de ce tutoriel est disponible en tant qu’exemple de pipeline.
>
>Pour le trouver, accédez à l’**interface visuelle dans votre espace de travail.** . Dans la section **Nouveau pipeline**, sélectionnez **Exemple 1 - Régression : Prédiction du prix de véhicules automobiles (de base)** .

## <a name="create-a-new-pipeline"></a>Créer un pipeline

Les pipelines Azure Machine Learning organisent plusieurs étapes dépendantes de machine learning et de traitement de données en une même ressource. Les pipelines vous permettent d’organiser, de gérer et de réutiliser des workflows de machine learning complexes entre des projets et des utilisateurs. Pour créer un pipeline Azure Machine Learning, vous devez disposer d’un espace de travail Azure Machine Learning service. Dans cette section, vous découvrez comment créer ces deux ressources.

### <a name="create-a-new-workspace"></a>Créer un espace de travail

Si vous disposez d’un espace de travail Azure Machine Learning service, passez à la section suivante.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-the-pipeline"></a>Créer le pipeline

1. Connectez-vous à [ml.azure.com](https://ml.azure.com) et sélectionnez l’espace de travail que vous voulez utiliser.

1. Sélectionnez **Interface visuelle**.

    ![Capture d’écran de l’espace de travail visuel montrant comment accéder à l’interface visuelle](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Sélectionnez **Modules prédéfinis faciles à utiliser**.

1. Sélectionnez le nom de pipeline par défaut **« Pipeline-Created-on ... »** en haut du canevas et remplacez-le par un nom significatif. Par exemple, **« Prédiction du prix de véhicules automobiles »** . Le nom n’a pas besoin d’être unique.

## <a name="import-data"></a>Importer des données

Un certain nombre d’exemples de jeux de données que vous pouvez expérimenter sont inclus dans l’interface visuelle. Pour les besoins de ce tutoriel, vous allez utiliser le jeu de données **Automobile price data (Raw)** (Données sur le prix des véhicules automobiles [brutes]). 

1. Sur la gauche du canevas de pipeline se trouve une palette de jeux de données et de modules. Sélectionnez **Jeux de données**, puis **Exemples** pour voir les exemples de jeux de données disponibles.

1. Sélectionnez le jeu de données **Automobile price data (raw)** , puis faites-le glisser sur le canevas.

   ![Faites glisser les données jusqu’au canevas](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualiser les données

Vous pouvez visualiser les données pour comprendre le jeu de données que vous allez utiliser.

1. Sélectionnez le module **Automobile price data (Raw)** .

1. Dans le volet **Propriétés** à droite du canevas, sélectionnez **Sorties**.

1. Sélectionnez l’icône de graphique pour visualiser les données.

    ![Visualiser les données](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Cliquez sur différentes colonnes dans la fenêtre de données pour visualiser des informations les concernant.

    Chaque ligne représente un véhicule automobile et chaque colonne représente une variable associée au véhicule automobile. Ce jeu de données contient 205 lignes et 26 colonnes.

## <a name="prepare-data"></a>Préparer les données

Les jeux de données nécessitent généralement un prétraitement avant l’analyse. Vous avez peut-être remarqué des valeurs manquantes lors de la visualisation du jeu de données. Pour que vous puissiez analyser les données correctement, ces valeurs manquantes doivent être nettoyées. Vous allez supprimer les colonnes où de nombreuses valeurs sont manquantes et supprimer les lignes où des valeurs sont manquantes.

### <a name="remove-a-column"></a>Supprimer une colonne

Quand vous entraînez un modèle, vous devez traiter le problème des données manquantes. Dans ce jeu de données, la colonne **normalized-losses** (pertes normalisées) a de nombreuses valeurs manquantes : vous allez donc l’exclure du modèle.

1. Sélectionner les colonnes de données à utiliser. Dans la zone de recherche située en haut de la palette, tapez **Sélectionner** afin de rechercher le module **Sélectionner des colonnes dans le jeu de données**.

1. Cliquez sur le module **Select Columns in Dataset** et faites-le glisser jusqu’au canevas. Déposez-le sous le module de jeu de données.

1. Connectez le jeu de données que vous avez ajouté précédemment au module **Select Columns in Dataset** en cliquant dessus et en le faisant glisser. Faites-le glisser depuis le port de sortie du jeu de données, qui est le petit cercle situé en bas du jeu de données sur le canevas, jusqu’au port d’entrée de **Select Columns in Dataset**, qui est le petit cercle en haut du module.

    > [!TIP]
    > Vous créez un flux de données à travers votre pipeline quand vous connectez le port de sortie d’un module au port d’entrée d’un autre module.
    >

    ![Connecter des modules](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Sélectionnez le module **Select Columns in Dataset**.

1. Dans le volet **Propriétés** à droite du canevas, sélectionnez **Paramètres** > **Modifier la colonne**.

1. Sélectionnez le signe **+** pour ajouter une règle.

1. Dans le menu déroulant, sélectionnez **Exclure** et **Noms des colonnes**.
    
1. Entrez **normalized-losses** dans la zone de texte.

1. En bas à droite, sélectionnez **Enregistrer** pour fermer le sélecteur de colonne.

    ![Exclure une colonne](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Le volet de propriétés montre que la colonne **normalized-losses** est exclue.

1. Sélectionnez le module **Select Columns in Dataset**. 

1. Dans **Propriétés**, sélectionnez **Paramètres** > **Commentaire** et entrez « Exclure les pertes normalisées ».

### <a name="clean-missing-data"></a>Nettoyage des données manquantes

Votre jeu de données a toujours des valeurs manquantes après la suppression de la colonne **normalized-losses**. Vous pouvez supprimer les données manquantes restantes en utilisant le module **Nettoyer les données manquantes**.

> [!TIP]
> Le nettoyage des valeurs manquantes dans les données d’entrée est un prérequis pour l’utilisation de la plupart des modules dans l’interface visuelle.

1. Tapez **Clean** dans la zone de recherche pour trouver le module **Clean Missing Data**.

1. Faites glisser le module **Nettoyer les données manquantes** jusqu’au canevas du pipeline et connectez-le au module **Sélectionner des colonnes dans le jeu de données**. 

1. Dans le volet des propriétés, sélectionnez **Remove entire row** (Supprimer la ligne entière) sous **Cleaning mode** (Mode nettoyage).

1. Dans le volet Propriétés, entrez « Supprimer les lignes avec des valeurs manquantes » dans la zone **Commentaire**.  

    Votre pipeline doit maintenant se présenter comme ceci :
    
    ![sélectionner une colonne](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Entraîner un modèle Machine Learning

Maintenant que les données sont prétraitées, vous pouvez construire un modèle prédictif. Vous allez utiliser vos données pour entraîner le modèle. Ensuite, vous testerez le modèle pour voir avec quelle précision il est capable de prédire les prix.

### <a name="select-an-algorithm"></a>Sélectionner un algorithme

La **classification** et la **régression** sont deux types d’algorithmes de machine learning supervisé. La **classification** permet de prédire une réponse à partir d'un jeu de catégories défini, comme une couleur (rouge, bleu ou vert). La **régression** est utilisée pour prédire un nombre.

Comme vous voulez prédire un prix, à savoir un nombre, vous pouvez utiliser un algorithme de régression. Dans cet exemple, vous utilisez un modèle de régression linéaire.

### <a name="split-the-data"></a>Fractionner les données

Utilisez vos données pour entraîner et tester le modèle en fractionnant les données en deux jeux de données distincts.

1. Tapez **fractionner les données** dans la zone de recherche pour localiser le module **Fractionner les données**, et connectez ce dernier au port de gauche du module **Nettoyer les données manquantes**.

1. Sélectionnez le module **Fractionner les données**.

1. Dans le volet Propriétés, définissez **Fraction de lignes dans le premier jeu de données de sortie** sur 0,7.

    Ceci a pour effet d’utiliser 70 % des données pour entraîner le modèle et de conserver 30 % pour le tester.

1. Dans le volet Propriétés, entrez « Fractionner le jeu de données en un jeu d’entraînement (0,7) et un jeu de test (0,3) » dans la zone **Commentaire**.

### <a name="train-the-model"></a>Formation du modèle

Formez le modèle en lui fournissant un jeu de données incluant le prix. Le modèle analyse les données et recherche des corrélations entre les caractéristiques d’une voiture et son prix.

1. Pour sélectionner l’algorithme d’apprentissage, effacez le contenu de la zone de recherche de votre palette de modules.

1. Développez **Algorithmes de Machine Learning**.
    
    Différentes catégories de modules s'affichent, permettant d'initialiser des algorithmes d'apprentissage automatique.

1. Pour ce pipeline, sélectionnez **Régression** > **Régression linéaire**, puis faites-le glisser jusqu’au canevas du pipeline.

1. Recherchez et faites glisser le module **Entraîner le modèle** jusqu’au canevas du pipeline. 

1. Connectez la sortie du module Régression linéaire à l’entrée gauche du module Entraîner le modèle.

1. Connectez la sortie des données d’entraînement (port de gauche) du module **Fractionner les données** à l’entrée droite du module **Entraîner le modèle**.

    ![Capture d’écran montrant la configuration correcte du module Entraîner le modèle Le module Régression linéaire se connecte au port gauche du module Entraîner le modèle et le module Fractionner les données se connecte au port droit du module Entraîner le modèle.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Sélectionnez le module **Entraîner le modèle**.

1. Dans le volet Propriétés, sélectionnez le sélecteur **Modifier la colonne**.

1. Dans la boîte de dialogue **Étiqueter une colonne**, développez le menu déroulant, puis sélectionnez **Noms des colonnes**. Dans la zone de texte, entrez **price** (prix). Le prix est la valeur que votre modèle va prédire.

    Votre pipeline doit se présenter comme suit :

    ![Capture d’écran montrant la configuration correcte du pipeline après l’ajout du module Entraîner le modèle.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Évaluer un modèle Machine Learning

Maintenant que vous avez entraîné le modèle en utilisant 70 % des données, vous pouvez l'utiliser pour le scoring des 30 % restants, afin de voir si votre modèle fonctionne.

1. Tapez **scorer le modèle** dans la zone de recherche pour localiser le module **Scorer le modèle**, puis faites-le glisser jusqu’au canevas du pipeline. 

1. Connectez la sortie du module **Entraîner le modèle** au port d’entrée de gauche du module **Scorer le modèle**. Connectez la sortie des données de test (port de droite) du module **Fractionner les données** au port d’entrée de droite du module **Scorer le modèle**.

1. Tapez **évaluer** dans la zone de recherche pour localiser le module **Évaluer le modèle**, puis faites-le glisser jusqu’au canevas du pipeline. 

1. Connectez la sortie du module **Scorer le modèle** à l’entrée de gauche du module **Évaluer le modèle**. 

    Le pipeline final doit maintenant se présenter comme ceci :

    ![Capture d’écran montrant la configuration correcte du pipeline](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Exécuter le pipeline

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Afficher les résultats

Une fois l’exécution terminée, vous pouvez voir les résultats de l’exécution du pipeline. 

1. Visualisez la sortie du module **Scorer le modèle** en sélectionnant le module **Scorer le modèle**.

1. Dans le volet **Propriétés**, sélectionnez **Sorties** > **Visualiser**. La sortie affiche les valeurs de prévision associées au prix, ainsi que les valeurs connues des données de test.

    ![Capture d’écran de la visualisation de la sortie qui met en évidence la colonne « Étiquette scorée »](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Visualisez la sortie du module **Évaluer le modèle** en sélectionnant le module **Scorer le modèle**.

1. Dans le volet **Propriétés**, sélectionnez **Sortie** > **Visualiser**, puis sélectionnez **Visualiser**.

Les statistiques suivantes s’affichent pour votre modèle :

* **Erreur absolue moyenne** : moyenne des erreurs absolues (une erreur correspond à la différence entre la valeur prédite et la valeur réelle).
* **Racine carrée de l’erreur quadratique moyenne** : la racine carrée de la moyenne des erreurs carrées des prévisions effectuées sur le jeu de données de test.
* **Erreur absolue relative**: la moyenne des erreurs absolues relative à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Erreur carrée relative** : la moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Coefficient de détermination** : aussi nommé valeur R au carré, il s’agit d’une mesure statistique indiquant à quel point un modèle correspond aux données.

Pour chacune des statistiques liées aux erreurs, les valeurs les plus petites sont privilégiées. En effet, une valeur plus petite indique un degré de correspondance plus étroit avec la valeur réelle. Plus la valeur du coefficient de détermination est proche de un (1,0), plus les prévisions sont correctes.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cette première partie du tutoriel, vous avez effectué les étapes suivantes :

* Créer un pipeline
* Préparer les données
* Entraîner le modèle
* Scorer et évaluer le modèle

Dans la deuxième partie, vous allez découvrir comment déployer votre modèle en tant que point de terminaison en temps réel.

> [!div class="nextstepaction"]
> [Continuer avec le déploiement des modèles](ui-tutorial-automobile-price-deploy.md)
