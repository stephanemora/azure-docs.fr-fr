---
title: 'Didacticiel : Prédire le prix de véhicules automobiles à l’aide de l’interface visuelle'
titleSuffix: Azure Machine Learning service
description: Découvrez comment entraîner, évaluer et déployer un modèle Machine Learning à l’aide d’une interface visuelle de type glisser-déplacer. Ce tutoriel constitue la première partie d’une série en deux volets sur la prédiction des prix de véhicules automobiles au moyen d’une régression linéaire.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720546"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Didacticiel : Prédire le prix de véhicules automobiles à l’aide de l’interface visuelle

Dans ce tutoriel, vous étudiez de manière approfondie le développement d’une solution prédictive dans l’interface visuelle du service Azure Machine Learning. À la fin de ce tutoriel, vous disposerez d’une solution capable de prédire le prix de n’importe quelle voiture en fonction des spécifications techniques que vous lui fournirez.

Dans la première partie du tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Importer et nettoyer les données
> * Entraîner un modèle Machine Learning
> * Scorer et évaluer un modèle

Dans la [deuxième partie](ui-tutorial-automobile-price-deploy.md) du tutoriel, vous allez apprendre à déployer votre modèle prédictif en tant que service web Azure.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Une version complète de ce tutoriel est disponible en tant qu’exemple d’expérience.

Pour le trouver, sélectionnez **Ajouter nouveau** dans la **page Expériences**, puis sélectionnez l’expérience **Sample 1 - Regression: Automobile Price Prediction(Basic)** (Exemple 1 - Régression : Prédiction du prix de véhicules automobiles [de base]).

## <a name="create-a-workspace"></a>Créer un espace de travail

Si vous disposez d’un espace de travail Azure Machine Learning service, passez à la [section suivante](#open-the-visual-interface-webpage).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Ouvrir la page web de l’interface visuelle

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).

1. Dans votre espace de travail, sélectionnez **Interface visuelle**. Ensuite, sélectionnez **Lancer l’interface visuelle**. 

    ![Capture d’écran du portail Azure montrant comment accéder à l’interface visuelle à partir d’un espace de travail du service Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>Créer votre première expérience

L’outil d’interface visuelle offre un espace visuel interactif qui vous permet de générer des modèles d’analytique prédictive. Faites glisser-déplacer des jeux de données et des modules d’analyse sur un canevas interactif, puis connectez-les ensemble pour créer une *expérience*.

1. Créez une expérience en sélectionnant **+Nouveau** en bas de la fenêtre d’interface visuelle.

    ![Ajouter une nouvelle expérience](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. Sélectionnez **Blank Experiment**.

1. Sélectionnez le nom d’expérience par défaut, **« Expérience créée le ... »** , situé en haut du canevas, et remplacez-le par un nom significatif. Par exemple, **Prédiction du prix de véhicules automobiles**. Le nom n’a pas besoin d’être unique.

## <a name="add-data"></a>Ajout de données

Les données sont la première chose dont vous avez besoin pour le machine learning. Vous pouvez utiliser plusieurs exemples de jeux de données inclus dans cette interface. Vous pouvez également importer des données à partir de sources existantes. Pour les besoins de ce tutoriel, vous allez utiliser le jeu de données **Automobile price data (Raw)** (Données sur le prix des véhicules automobiles [brutes]). 

1. Sur la gauche de la zone de dessin de l’expérience se trouve une palette de jeux de données et de modules. Sélectionnez **Saved Datasets** (Jeux de données enregistrés), puis **Samples** (Exemples) pour voir les exemples de jeux de données disponibles.

1. Sélectionnez le jeu de données **Automobile price data (raw)** , puis faites-le glisser jusqu’au canevas.

   ![Faites glisser les données jusqu’au canevas](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Select columns

Sélectionner les colonnes de données à utiliser. Pour commencer, configurez le module pour qu’il affiche toutes les colonnes disponibles.

> [!TIP]
> Si vous connaissez le nom des données ou du module que vous voulez, utilisez la barre de recherche en haut de la palette pour les retrouver rapidement. Ce raccourci sera utilisé dans le reste du tutoriel.


1. Tapez **Select** dans la zone de recherche pour trouver le module **Select Columns in Dataset**.

1. Cliquez sur **Select Columns in Dataset** et faites-glisser le module jusqu’au canevas. Déposez-le en dessous du jeu de données que vous avez ajouté précédemment.

1. Connectez le jeu de données à **Select Columns in Dataset** : cliquez sur le port de sortie du jeu de données, faites glisser la souris jusqu’au port d’entrée de **Select Columns in Dataset**, puis relâchez le bouton de la souris. Le jeu de données et le module restent connectés même si vous opérez des déplacements sur le canevas.

    > [!TIP]
    > Les jeux de données et les modules disposent de ports d’entrée et de sortie représentés par de petits cercles : les ports d’entrée se situent en haut, tandis que les ports de sortie se situent en bas. Vous créez un flux de données dans votre expérience quand vous connectez le port de sortie d’un module au port d’entrée d’un autre module.
    >

    ![Connecter des modules](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Le point d’exclamation rouge indique que vous n’avez pas encore défini les propriétés du module.

1. Sélectionnez le module **Select Columns in Dataset**.

1. Dans le volet **Propriétés** à droite du canevas, sélectionnez **Modifier les colonnes**.

    Dans la boîte de dialogue **Select columns** (Sélectionner les colonnes), sélectionnez **ALL COLUMNS** (TOUTES LES COLONNES) et incluez **toutes les fonctionnalités**. La boîte de dialogue doit ressembler à ceci :

     ![sélecteur de colonne](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. En bas à droite, sélectionnez le bouton **OK** pour fermer le sélecteur de colonne.

## <a name="run-the-experiment"></a>Exécuter l’expérience

À tout moment, cliquez sur le port de sortie d’un jeu de données ou d’un module pour examiner l’aspect des données à ce stade dans le flux de données. Si l’option **Visualize** (Visualiser) est désactivée, vous devez d’abord exécuter l’expérience.

Une expérience s’exécute sur une cible de calcul, qui est une ressource de calcul attachée à votre espace de travail. Une fois que vous avez créé une cible de calcul, vous pouvez la réutiliser pour d’autres exécutions ultérieures.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Une fois que la cible de calcul est disponible, l’expérience s’exécute. Une fois l’exécution terminée, une coche verte apparaît sur chaque module.


## <a name="preview-the-data"></a>Prévisualiser les données

Maintenant que vous avez exécuté votre expérience initiale, vous pouvez visualiser les données pour mieux comprendre le jeu de données que vous devez utiliser.

1. Sélectionnez le port de sortie au bas du module **Select Columns in Dataset**, puis sélectionnez **Visualize**.

1. Cliquez sur différentes colonnes dans la fenêtre de données pour afficher des informations les concernant.

    Dans ce jeu de données, chaque ligne représente un véhicule automobile et chaque colonne représente une variable associée au véhicule automobile. Ce jeu de données contient 205 lignes et 26 colonnes.

     Chaque fois que vous cliquez sur une colonne de données, les informations **statistiques** et l’image de **visualisation** de la colonne en question s’affichent à gauche. Par exemple, quand vous cliquez sur **num-of-doors** (nombre de portes), vous constatez qu’elle contient 2 valeurs uniques et 2 valeurs manquantes. Faites défiler vers le bas pour afficher les valeurs : deux et quatre (portes).

     ![Prévisualiser les données](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Cliquez sur chaque colonne pour obtenir plus d’informations sur votre jeu de données, et déterminez si ces colonnes seront utiles pour prédire le prix d’une voiture.

## <a name="prepare-data"></a>Préparer les données

Pour pouvoir être analysé, un jeu de données nécessite généralement un traitement préalable. Vous avez peut-être remarqué des valeurs manquantes lors de la visualisation du jeu de données. Pour que vous puissiez analyser les données correctement, ces valeurs manquantes doivent être nettoyées. Vous allez supprimer les lignes dans lesquelles il manque des valeurs. Par ailleurs, la colonne **normalized-losses** (pertes normalisées) contient une grande proportion de valeurs manquantes. Vous allez donc l’exclure du modèle.

> [!TIP]
> Le nettoyage des valeurs manquantes des données d’entrée est un prérequis pour l’utilisation de la plupart des modules.

### <a name="remove-column"></a>Supprimer une colonne

Commencez par supprimer entièrement la colonne **normalized-losses**.

1. Sélectionnez le module **Select Columns in Dataset**.

1. Dans le volet **Propriétés** à droite du canevas, sélectionnez **Modifier les colonnes**.

    * Laissez **With rules** (Avec règles) et **All columns** (Toutes les colonnes) sélectionnés.

    * Dans les listes déroulantes, sélectionnez **Exclure** et **Noms des colonnes**, puis cliquez dans la zone de texte. Tapez **normalized-losses**.

    * En bas à droite, sélectionnez le bouton **OK** pour fermer le sélecteur de colonne.

    ![Exclure une colonne](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    À présent, le volet de propriétés du module Select Columns in Dataset indique qu’il transmettra toutes les colonnes du jeu de données, à l’exception de **normalized-losses**.
        
    Le volet de propriétés montre que la colonne **normalized-losses** est exclue.
        
    ![Volet de propriétés](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Vous pouvez ajouter un commentaire dans un module en double-cliquant sur ce module, puis en saisissant du texte. Ceci peut vous aider à voir d'un seul coup d'œil ce que fait chaque module dans votre expérience. 

1. Double-cliquez sur le module **Select Columns in Dataset** et tapez le commentaire « Exclude normalized losses ». 
    
    Après avoir tapé le commentaire, cliquez à l’extérieur du module. Une flèche vers le bas s’affiche pour indiquer que le module contient un commentaire.

1. Cliquez sur la flèche vers le bas pour afficher le commentaire.

    Le module présente maintenant une flèche vers le haut pour masquer le commentaire.
        
    ![Commentaires](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Nettoyage des données manquantes

Lorsque vous entraînez un modèle, vous devez traiter le problème des données manquantes. Dans ce cas, vous allez ajouter un module pour supprimer toutes les lignes restantes dans lesquelles il manque des données.

1. Tapez **Clean** dans la zone de recherche pour trouver le module **Clean Missing Data**.

1. Faites glisser le module **Clean Missing Data** jusqu’au canevas de l’expérience et connectez-le au module **Select Columns in Dataset**. 

1. Dans le volet des propriétés, sélectionnez **Remove entire row** (Supprimer la ligne entière) sous **Cleaning mode** (Mode nettoyage).

    Ces options indiquent au module **Clean Missing Data** de nettoyer les données en supprimant les lignes dans lesquelles il manque des valeurs.

1. Double-cliquez sur le module et saisissez le commentaire suivant : « Supprimer les lignes de valeur manquantes ».
 
    ![Supprimer les lignes](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    Votre expérience doit maintenant se présenter comme suit :
    
    ![sélectionner une colonne](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualiser les résultats

Comme vous avez apporté des modifications aux modules de votre expérience, l’état est passé à « In draft » (Brouillon).  Pour visualiser les nouvelles données nettoyées, vous devez d’abord réexécuter l’expérience.

1. Sélectionnez **Run** (Exécuter) au bas pour exécuter l’expérience.

    Cette fois, vous pouvez réutiliser la cible de calcul que vous avez créée précédemment.

1. Sélectionnez **Run** (Exécuter) dans la boîte de dialogue.

   ![Exécuter une expérience](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. Quand l’exécution se termine, cliquez sur le module **Clean Missing Data** pour visualiser les nouvelles données nettoyées.

    ![Visualiser les données nettoyées](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. Cliquez sur différentes colonnes dans la fenêtre des données nettoyées pour voir comment les données ont changé.

    ![Visualiser les données nettoyées](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    Il existe désormais 193 lignes et 25 colonnes.

    Quand vous cliquez sur **num-of-doors**, vous constatez qu’il reste 2 valeurs uniques mais aucune valeur manquante. Cliquez sur les autres colonnes pour vérifier qu’il ne manque aucune valeur dans le jeu de données. 

## <a name="train-the-model"></a>Formation du modèle

Maintenant que les données sont prêtes, vous pouvez construire un modèle prédictif. Vous allez utiliser vos données pour entraîner le modèle. Ensuite, vous testerez le modèle pour voir avec quelle précision il est capable de prédire les prix.

La **classification** et la **régression** sont deux types d’algorithmes de machine learning supervisé. La **classification** permet de prédire une réponse à partir d'un jeu de catégories défini, comme une couleur (rouge, bleu ou vert). La **régression** est utilisée pour prédire un nombre.

Comme vous voulez prédire un prix, à savoir un nombre, vous pouvez utiliser un algorithme de régression. Dans cet exemple, vous utilisez un modèle de régression linéaire.

Formez le modèle en lui fournissant un jeu de données incluant le prix. Le modèle analyse les données et recherche des corrélations entre les caractéristiques d’une voiture et son prix. Ensuite, testez le modèle en lui affectant un ensemble de caractéristiques automobiles qu’il connaît et constatez avec quelle précision le modèle parvient à prédire le prix connu.

Utilisez vos données pour entraîner et tester le modèle en fractionnant les données en jeux de données distincts d’entraînement et de test.

1. Tapez **fractionner les données** dans la zone de recherche pour localiser le module **Fractionner les données**, et connectez ce dernier au port de gauche du module **Nettoyer les données manquantes**.

1. Sélectionnez le module **Fractionner les données** que vous venez de connecter. Dans le volet Propriétés, affectez la valeur 0,7 au paramètre Fraction de lignes dans le premier jeu de données de sortie. De cette façon, vous utiliserez 70 % des données pour entraîner le modèle et conserverez 30 % pour le tester.

    ![Capture d’écran montrant la configuration correcte du volet Propriétés Les valeurs de « Split Data » (Fractionner les données) doivent être « Split Rows » (Lignes fractionnées), 0,7, Randomized split (Fractionnement aléatoire), 0, Faux.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Double-cliquez sur **Split Data** (Fractionner les données) et tapez le commentaire « Fractionner le jeu de données en jeu d’entraînement (0,7) et jeu de test (0,3) ».

1. Pour sélectionner l’algorithme d’apprentissage, effacez le contenu de la zone de recherche de votre palette de modules.

1. Développez **Machine Learning**, puis **Initialiser le modèle**. Différentes catégories de modules s'affichent, permettant d'initialiser des algorithmes d'apprentissage automatique.

1. Pour les besoins de cette expérience, sélectionnez **Régression** > **Régression linéaire**, puis faites-le glisser jusqu’à la zone de dessin.

    ![Capture d’écran montrant la configuration correcte du volet Propriétés Les valeurs de « Split Data » (Fractionner les données) doivent être « Split Rows » (Lignes fractionnées), 0,7, Randomized split (Fractionnement aléatoire), 0, Faux.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Recherchez et faites glisser le module **Entraîner le modèle** jusqu’à la zone de dessin. Connectez la sortie du module Régression linéaire à l’entrée de gauche du module Entraîner le modèle, puis connectez la sortie des données d’entraînement (port gauche) du module **Fractionner les données** à l’entrée de droite du module **Entraîner le modèle**.

    ![Capture d’écran montrant la configuration correcte du module Entraîner le modèle Le module Régression linéaire se connecte au port gauche du module Entraîner le modèle et le module Fractionner les données se connecte au port droit du module Entraîner le modèle.](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Sélectionnez le module **Entraîner le modèle**. Dans le volet Propriétés, sélectionnez Lancer le sélecteur de colonne, puis tapez **price** (prix) en regard de **Include column names** (Inclure noms des colonnes). Le prix est la valeur que votre modèle va prédire.

    ![Capture d’écran montrant la configuration correcte du module de sélection de colonne With rules (Avec règles) > Include column names (Inclure noms des colonnes) > "price" (prix)](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Votre expérience doit ressembler à cela :

    ![Capture d’écran montrant la configuration correcte de l’expérience après l’ajout du module Entraîner le modèle.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Exécuter l’expérience d’entraînement

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Scorer et évaluer le modèle

Maintenant que vous avez entraîné le modèle en utilisant 70 % des données, vous pouvez l'utiliser pour le scoring des 30 % restants, afin de voir si votre modèle fonctionne.

1. Tapez **scorer le modèle** dans la zone de recherche pour localiser le module **Scorer le modèle**, puis faites-le glisser jusqu’à la zone de dessin. Connectez la sortie du module **Entraîner le modèle** au port d’entrée de gauche du module **Scorer le modèle**. Connectez la sortie des données de test (port de droite) du module **Fractionner les données** au port d’entrée de droite du module **Scorer le modèle**.

1. Tapez **évaluer** dans la zone de recherche pour localiser le module **Évaluer le modèle**, puis faites-le glisser jusqu’à la zone de dessin. Connectez la sortie du module **Scorer le modèle** à l’entrée de gauche du module **Évaluer le modèle**. L’expérience finale doit ressembler à ceci :

    ![Capture d’écran montrant la configuration finale correcte de l’expérience](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Exécutez l’expérience à l’aide de la cible de calcul utilisée précédemment.

1. Affichez la sortie du module **Scorer le modèle** en sélectionnant le port de sortie du module **Scorer le modèle**, et sélectionnez **Visualiser**. La sortie affiche les valeurs de prévision associées au prix, ainsi que les valeurs connues des données de test.

    ![Capture d’écran de la visualisation de la sortie qui met en évidence la colonne « Étiquette scorée »](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Pour afficher la sortie du module Évaluer le modèle, sélectionnez le port de sortie, puis sélectionnez Visualiser.

    ![Capture d’écran montrant les résultats d’évaluation de l’expérience finale](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Les statistiques suivantes s’affichent pour votre modèle :

* **Erreur absolue moyenne** : moyenne des erreurs absolues (une erreur correspond à la différence entre la valeur prédite et la valeur réelle).
* **Racine carrée de l’erreur quadratique moyenne** : la racine carrée de la moyenne des erreurs carrées des prévisions effectuées sur le jeu de données de test.
* **Erreur d’absolue relative** : la moyenne des erreurs absolues relative à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Erreur carrée relative** : la moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
* **Coefficient de détermination** : aussi nommé valeur R au carré, il s’agit d’une mesure statistique indiquant à quel point un modèle correspond aux données.

Pour chacune des statistiques liées aux erreurs, les valeurs les plus petites sont privilégiées. En effet, une valeur plus petite indique un degré de correspondance plus étroit avec la valeur réelle. Plus la valeur du coefficient de détermination est proche de un (1,0), plus les prévisions sont correctes.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Gérer les expériences dans l’espace de travail du service Azure Machine Learning

Les expériences que vous créez dans l’interface visuelle peuvent être gérées à partir de l’espace de travail du service Azure Machine Learning. Utilisez cet espace de travail pour afficher des informations plus détaillées, telles que les exécutions individuelles d’expérience, les journaux de diagnostic, les graphiques d’exécution et plus encore.

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).  

1. Dans votre espace de travail, sélectionnez **Expériences**. Sélectionnez ensuite l’expérience que vous avez créée.

    ![Capture d’écran montrant comment accéder à des expériences dans le portail Azure](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Dans cette page, vous verrez une vue d’ensemble de l’expérience et de ses dernières exécutions.

    ![Capture d’écran montrant une vue d’ensemble des statistiques d’expérience dans le portail Azure](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Sélectionnez un numéro d’exécution pour afficher plus d’informations sur une exécution spécifique.

    ![Capture d’écran d’un rapport d’exécution détaillé](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Le rapport d’exécution est mis à jour en temps réel. Si vous avez utilisé un module **Exécuter un script Python** dans votre expérience, vous pouvez spécifier les journaux de script à fournir en sortie dans l’onglet **Journaux**.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans cette première partie du tutoriel, vous avez effectué les étapes suivantes :

* Créer une expérience
* Préparer les données
* Formation du modèle
* Scorer et évaluer le modèle

Dans la deuxième partie, vous apprendrez à déployer votre modèle en tant que service web Azure.

> [!div class="nextstepaction"]
> [Continuer avec le déploiement des modèles](ui-tutorial-automobile-price-deploy.md)
