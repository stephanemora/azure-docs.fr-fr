---
title: Informations de référence sur les algorithmes et les modules
titleSuffix: Azure Machine Learning service
description: En savoir plus sur les modules disponibles dans l’interface visuelle Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145132"
---
# <a name="algorithm--module-reference-overview"></a>Vue d’ensemble de référence algorithme & module

Ce contenu de référence présente les compétences techniques sur chaque apprentissage algorithmes et les modules disponibles dans l’interface visuelle (version préliminaire) du service Azure Machine Learning.

Chaque module représente un ensemble de code qui peut s’exécuter indépendamment et effectuer un tâche de machine learning, étant donné les entrées requises. Un module peut contenir un algorithme particulier, ou effectuer une tâche qui est importante dans l’apprentissage automatique, telles que le remplacement de valeur manquant, ou l’analyse statistique.

> [!TIP]
> Dans n’importe quelle expérience dans l’interface visuelle, vous pouvez obtenir des informations sur un module spécifique. Sélectionnez le module, puis sélectionnez le **aide plus** lien dans le **aide rapide** volet.

## <a name="modules"></a>Modules

Les modules sont organisés par fonctionnalités :

| Fonctionnalités | Description | Module |
| --- |--- | ---- |
| Conversions de format de données | Convertir des données entre différents formats de fichier utilisés dans l’apprentissage automatique, | [Convertir en fichier CSV](convert-to-csv.md) |
| Entrée de données et de sortie | Déplacer des données à partir de sources cloud dans votre expérience. Écrire vos résultats ou des données intermédiaires à stockage Azure, une base de données SQL ou Hive, lors de l’exécution d’une expérience, ou utiliser le stockage cloud d’échanger des données entre les expériences.  | [Importer des données](import-data.md)<br/>[Exporter les données](export-data.md)<br/>[Entrer manuellement les données](enter-data-manually.md) |
| Transformation des données | Opérations sur les données qui sont uniques à l’apprentissage automatique, telles que la normalisation ou le compartimentage de données, la sélection de fonctionnalité et la réduction de dimensionnalité.| [Sélectionner des colonnes dans le jeu de données](select-columns-in-dataset.md) <br/> [Modifier les métadonnées](edit-metadata.md) <br/> [Nettoyage des données manquantes](clean-missing-data.md) <br/> [Ajouter des colonnes](add-columns.md) <br/> [Ajouter des lignes](add-rows.md) <br/> [Supprimer les lignes en double](remove-duplicate-rows.md) <br/> [Fractionner les données](split-data.md) <br/> [Normaliser les données](normalize-data.md) <br/> [Partition and Sample](partition-and-sample.md) |
| Module Python | Écrire du code et l’incorporer dans un module à intégrer Python à votre expérience. | [Exécutez le Script Python](execute-python-script.md)   <br/> [Créer le modèle Python](create-python-model.md)
|  | **Algorithmes d’apprentissage**: | |
| classification ; | Prédire une classe.  Choisissez à partir du fichier binaire (à deux classes) ou algorithmes multiclasses.| [Forêt de décision multiclasse](multiclass-decision-forest.md) <br/> [Régression logistique multiclasse](multiclass-logistic-regression.md)  <br/> [Réseau neuronal multiclasse](multiclass-neural-network.md)  <br/>  [Two-Class Logistic Regression](two-class-logistic-regression.md)  <br/>[Perceptron Moyenné Two-Class](two-class-averaged-perceptron.md) <br/> [Two-Class&nbsp;Boosted&nbsp;décision&nbsp;arborescence](two-class-boosted-decision-tree.md)  <br/> [Two-Class Decision Forest](two-class-decision-forest.md)  <br/> [Two-Class Neural Network](two-class-neural-network.md)  <br/> [Deux&#8209;classe&nbsp;prise en charge&nbsp;vecteur&nbsp;Machine](two-class-support-vector-machine.md) 
| Clustering | Regrouper les données.| [Clustering K-Means](k-means-clustering.md)
| régression ; | Prédire une valeur. | [Régression linéaire](linear-regression.md)  <br/> [Régression de réseau neuronal](neural-network-regression.md)  <br/> [Régression de forêts de décision](decision-forest-regression.md)  <br/> [Boosted&nbsp;décision&nbsp;arborescence&nbsp;régression](boosted-decision-tree-regression.md)
|  | **Créer et évaluer des modèles**: | |
| Former   | Exécuter des données au moyen de l’algorithme. | [Former le modèle](train-model.md)  <br/> [Former le modèle de Clustering](train-clustering-model.md)    |
| Évaluer le modèle | Mesurer la précision du modèle formé. |  [Évaluer le modèle](evaluate-model.md)
| Score | Obtenir des prédictions à partir du modèle que vous avez formé simplement. | [Appliquer une Transformation](apply-transformation.md)<br/>[Affecter&nbsp;données&nbsp;à&nbsp;Clusters](assign-data-to-clusters.md) <br/>[Noter le modèle](score-model.md)

## <a name="error-messages"></a>messages d'erreur

En savoir plus sur la [messages d’erreur et les codes d’exception](machine-learning-module-error-codes.md) que vous pouvez rencontrer à l’aide de modules dans l’interface visuelle de service Azure Machine Learning.
