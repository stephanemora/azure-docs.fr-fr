---
title: 'Démarrage rapide : Utiliser un notebook échantillon de la galerie Synapse Analytics'
description: Découvrez comment utiliser un exemple notebook échantillon de la galerie Synapse Analytics pour explorer les données et créer un modèle Machine Learning.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: ''
ms.date: 06/11/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: 3fadfc3fdead7249480b2ce841601149f750b958
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063632"
---
# <a name="quickstart-use-a-sample-notebook-from-the-synapse-analytics-gallery"></a>Démarrage rapide : Utiliser un notebook échantillon de la galerie Synapse Analytics

Ce démarrage rapide montre comment copier un notebook échantillon de Machine Learning de la galerie Synapse Analytics dans votre espace de travail, le modifier et l’exécuter.
Le notebook échantillon ingère un jeu de données ouvert de trajets de taxi à New York, et utilise une visualisation pour vous aider à préparer les données. Ensuite, il effectue l’apprentissage d’un modèle pour prédire s’il y aura un pourboire sur un trajet donné.

Ce notebook présente les étapes de base à suivre pour créer un modèle : **importation des données**, **préparation des données**, **apprentissage du modèle** et **évaluation**. Vous pouvez utiliser cet échantillon comme point de départ pour créer un modèle avec vos propres données.

## <a name="prerequisites"></a>Prérequis

* [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
* [Pool Spark](../get-started-analyze-spark.md) dans votre espace de travail Azure Synapse Analytics.

## <a name="copy-the-notebook-to-your-workspace"></a>Copier le notebook dans votre espace de travail

1. Ouvrez votre espace de travail et sélectionnez **Learn** dans la page d’accueil.
1. Dans le **Centre de connaissances**, sélectionnez **Parcourir la galerie**.
1. Dans la Galerie, sélectionnez **Notebooks**.
1. Recherchez et sélectionnez le notebook « Data Exploration and ML Modeling - NYC taxi predict using Spark MLib » (Exploration de données et modélisation ML - Prédiction de taxi à New York à l’aide de Spark MLib).

   :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-select-ml-notebook.png" alt-text="Sélectionnez le notebook échantillon de Machine Learning dans la galerie.":::

1. Sélectionnez **Continuer**.
1. Sur la page d’aperçu du notebook, sélectionnez **Ouvrir le notebook**. Le notebook échantillon est copié et ouvert dans votre espace de travail.

    :::image type="content" source="media\quickstart-gallery-sample-notebook\gallery-open-ml-notebook.png" alt-text="Ouvrez le notebook échantillon de Machine Learning dans votre espace de travail.":::

1. Dans le menu **Attacher à** du notebook ouvert, sélectionnez votre pool Apache Spark.

## <a name="run-the-notebook"></a>Exécuter le bloc-notes

Le notebook divisé en plusieurs cellules exécutant chacune une fonction spécifique.
Vous pouvez exécuter manuellement chaque cellule de manière séquentielle, ou sélectionner **Exécuter tout** pour exécuter toutes les cellules.

Voici les descriptions de chacune des cellules du notebook :

1. Importez les fonctions PySpark que le notebook utilise.
1. **Date d’ingestion** : ingérez les données à partir du jeu de données ouvert Azure **NycTlcYellow** dans une tramedonnées locale à des fins de traitement. Le code extrait les données d’une période spécifique. Vous pouvez modifier les dates de début et de fin pour obtenir des données différentes.
1. Sous-échantillonnez le jeu de données pour accélérer le développement. Vous pouvez modifier cette étape pour changer la taille de l’échantillon ou la valeur de départ de l’échantillonnage.
1. **Analyse exploratoire des données** : affichez des graphiques pour visualiser les données. Vous pouvez ainsi vous faire une idée de la préparation des données nécessaire avant de créer le modèle.
1. **Préparation et caractérisation des données** : filtrez les données hors norme découvertes via la visualisation et créez des variables dérivées utiles.
1. **Préparation des données et caractérisation, partie 2** : supprimez les colonnes inutiles et créez des caractéristiques supplémentaires.
1. **Encodage** : convertissez les variables chaînes en nombres que le modèle de régression logistique attend.
1. **Génération de jeux de données de test et d’apprentissage** : fractionnez les données en jeux de données de test et d’apprentissage séparés. Vous pouvez modifier la fraction et la valeur de départ aléatoire utilisée pour fractionner les données.
1. **Apprentissage du modèle** : effectuez l’apprentissage d’un modèle de régression logistique et affichez sa métrique « Zone sous ROC » pour voir comment le modèle fonctionne. Cette étape enregistre également le modèle formé au cas où vous souhaiteriez l’utiliser ailleurs.
1. **Évaluation et visualisation** : tracez la courbe ROC du modèle pour évaluer celui-ci davantage.

## <a name="save-the-notebook"></a>Enregistrer le notebook

Pour enregistrer votre notebook, sélectionnez **Publier** dans la barre de commandes de l’espace de travail.

## <a name="copying-the-sample-notebook"></a>Copie du notebook échantillon

Pour effectuer une copie de ce notebook, cliquez sur les points de suspension dans la barre de commandes supérieure, puis sélectionnez **Cloner** pour créer une copie dans votre espace de travail, ou **Exporter** pour télécharger une copie du fichier de notebook (`.ipynb`).

:::image type="content" source="media\quickstart-gallery-sample-notebook\copy-notebook.png" alt-text="Effectuez une copie du notebook avec la commande Exporter ou Cloner.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour vous assurer que l’instance Spark est arrêtée quand vous avez terminé, mettez fin aux sessions connectées (notebooks). Le pool s’arrête quand la **durée d’inactivité** spécifiée dans le pool Apache Spark est atteinte. Vous pouvez également sélectionner **Arrêter la session** dans la barre d’état en haut à droite du notebook.

:::image type="content" source="media\quickstart-gallery-sample-notebook\stop-session.png" alt-text="Arrêtez la session.":::

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez d’autres notebooks échantillons Synapse dans GitHub](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning)
* [Machine Learning avec Apache Spark](../spark/apache-spark-machine-learning-concept.md)
