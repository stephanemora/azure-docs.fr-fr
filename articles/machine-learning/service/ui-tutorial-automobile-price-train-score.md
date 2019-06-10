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
ms.openlocfilehash: e37e99323c92adad0b9e897af8c276a8ac153371
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515630"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Didacticiel : Prédire le prix de véhicules automobiles à l’aide de l’interface visuelle

Dans ce tutoriel, vous étudiez de manière approfondie le développement d’une solution prédictive dans l’interface visuelle du service Azure Machine Learning. À la fin de ce tutoriel, vous disposerez d’une solution capable de prédire le prix de n’importe quelle voiture en fonction des spécifications techniques que vous lui fournirez.

Ce tutoriel [suit le guide de démarrage rapide](ui-quickstart-run-experiment.md) et constitue le **premier volet d’une série de deux tutoriels**. Toutefois, vous n’êtes pas obligé de suivre le guide de démarrage rapide avant de commencer.

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Importer et nettoyer les données (mêmes étapes que dans le guide de démarrage rapide)
> * Entraîner un modèle Machine Learning
> * Scorer et évaluer un modèle

Dans la [deuxième partie](ui-tutorial-automobile-price-deploy.md) de cette série de tutoriels, vous apprendrez à déployer votre modèle prédictif en tant que service web Azure.

> [!NOTE]
> Une version complète de ce tutoriel est disponible en tant qu’exemple d’expérience.
> Dans la page Expériences, accédez à **Ajouter nouveau** > **Exemple 1 - Régression : Prédiction du prix de véhicules automobiles (de base)**


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

## <a name="create-a-workspace"></a>Créer un espace de travail

Si vous disposez d’un espace de travail Azure Machine Learning service, passez à la [section suivante](#open-the-visual-interface-webpage). Dans le cas contraire, créez-en un maintenant.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Ouvrir la page web de l’interface visuelle

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).  

1. Dans votre espace de travail, sélectionnez **Interface visuelle**.  Ensuite, sélectionnez **Lancer l’interface visuelle**.  

    ![Capture d’écran du portail Azure montrant comment accéder à l’interface visuelle à partir d’un espace de travail du service Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    La page web d’interface s’affiche dans une nouvelle page de navigateur.  

## <a name="import-and-clean-your-data"></a>Importer et nettoyer les données

Pour commencer, vous devez nettoyer vos données. Si vous avez terminé le démarrage rapide, vous pouvez réutiliser ici votre expérience de préparation de données. Si vous n’avez pas terminé le démarrage rapide, ignorez la section suivante et [commencez à partir d’une nouvelle expérience](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Réutiliser l’expérience de démarrage rapide

1. Ouvrez votre expérience de démarrage rapide.

1. Sélectionnez **Enregistrer sous** en bas de la fenêtre.

1. Attribuez-lui un nouveau nom dans la boîte de dialogue contextuelle qui s’affiche.

    ![Capture d’écran montrant comment renommer une expérience en « Tutorial - Predict Automobile Price » (Tutoriel - Prédire le prix de véhicules automobiles)](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. L'expérience doit ressembler à ceci :

    ![Capture d’écran montrant l’état attendu de l’expérience. Le jeu de données automobiles se connecte au module Sélectionner des colonnes qui se connecte au module Nettoyer les données manquantes](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Si vous avez réutilisé avec succès votre expérience de démarrage rapide, ignorez la section suivante pour commencer la [formation de votre modèle](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Commencer à partir d’une nouvelle expérience

Si vous n’avez pas suivi le guide de démarrage rapide, suivez ces étapes pour créer rapidement une expérience qui importe et nettoie le jeu de données automobiles.

1. Créez une expérience en sélectionnant **+Nouveau** en bas de la fenêtre d’interface visuelle.

1. Sélectionnez **Expériences** >  **Expérience vide**.

1. Sélectionnez le nom d’expérience par défaut, **« Expérience créée le ... »** , situé en haut du canevas, et remplacez-le par un nom significatif. Par exemple, **Prédiction du prix de véhicules automobiles**. Le nom n’a pas besoin d’être unique.

1. Sur la gauche de la zone de dessin de l’expérience se trouve une palette de jeux de données et de modules. Pour rechercher des modules, utilisez la zone de recherche en haut de la palette des modules. Tapez la valeur **automobile** dans la zone de recherche pour localiser le jeu de données **Données sur le prix des véhicules automobiles (brutes)** . Faites glisser ce jeu de données vers le canevas de l’expérience.

    ![Capture d’écran montrant comment rechercher le jeu de données des prix des véhicules automobiles](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    À présent que vous avez ces données, vous pouvez ajouter un module qui supprimera complètement la colonne **normalized-losses**. Ensuite, ajoutez un autre module qui supprimera toutes les lignes où il manque des données.

1. Tapez la chaîne **sélectionner des colonnes** dans la zone de recherche pour localiser le module **Sélectionner des colonnes dans le jeu de données**. Faites ensuite glisser ce module vers le canevas de l’expérience. Ce module permet de sélectionner les colonnes de données à inclure dans le modèle ou à en exclure.

1. Connectez le port de sortie du jeu de données **Données sur le prix des véhicules automobiles (brutes)** au port d’entrée de Sélectionner des colonnes dans le jeu de données.

    ![Image GIF animée montrant comment connecter le module de données de prix des véhicules automobiles au module Sélectionner des colonnes](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Sélectionnez le module Sélectionner des colonnes dans le jeu de données et sélectionnez **Lancer le sélecteur de colonne** dans le volet **Propriétés**.

   1. À gauche, sélectionnez **With rules** (Avec règles).

   1. En regard de **Commencer par**, sélectionnez **Toutes les colonnes**. Ces règles indiquent au module **Sélectionner des colonnes dans le jeu de données** de transmettre toutes les colonnes, sauf celles que nous nous apprêtons à exclure.

   1. Dans les listes déroulantes, sélectionnez **Exclure** et **noms des colonnes**, puis tapez **normalized-losses** dans la zone de texte.

   1. Sélectionnez le bouton OK pour fermer le sélecteur de colonne (en bas à droite).

     À présent, le volet de propriétés du module **Sélectionner des colonnes dans le jeu de données** indique qu’il transmettra toutes les colonnes du jeu de données, à l’exception de **normalized-losses**.

1. Ajoutez un commentaire dans le module **Sélectionner des colonnes dans le jeu de données** en double-cliquant sur le module, et entrez « Exclure les pertes normalisées. ». Ceci peut vous aider à voir d'un coup d'œil ce que fait le module dans votre expérience.

    ![Capture d’écran montrant la configuration correcte du module Sélectionner des colonnes](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Tapez **Nettoyer** dans la zone de recherche pour localiser le module **Nettoyer les données manquantes**. Faites glisser le module **Nettoyer les données manquantes** jusqu’à la zone de dessin de l’expérience et connectez-le au module **Sélectionner des colonnes dans le jeu de données**.

1. Dans le volet **Propriétés**, sélectionnez **Supprimer toute la ligne** sous **Mode de nettoyage**. Ces options indiquent au module **Nettoyer les données manquantes** de nettoyer les données en supprimant les lignes où il manque des valeurs. Double-cliquez sur le module et saisissez le commentaire suivant : « Supprimer les lignes de valeur manquantes ».

![Capture d’écran montrant la configuration correcte du module Nettoyer les données manquantes](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

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

    À présent, l'expérience doit ressembler à ceci :
    ![Capture d’écran montrant la configuration correcte de l’expérience après l’ajout du module Entraîner le modèle](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

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

* Réutiliser l’expérience créée dans le guide de démarrage rapide
* Préparer les données
* Formation du modèle
* Scorer et évaluer le modèle

Dans la deuxième partie, vous apprendrez à déployer votre modèle en tant que service web Azure.

> [!div class="nextstepaction"]
> [Continuer avec le déploiement des modèles](ui-tutorial-automobile-price-deploy.md)
