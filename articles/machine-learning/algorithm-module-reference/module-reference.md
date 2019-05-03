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
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029339"
---
# <a name="algorithm--module-reference-overview"></a>Vue d’ensemble de référence algorithme & module

Ce contenu de référence présente les compétences techniques sur chaque apprentissage algorithmes et les modules disponibles dans l’interface visuelle (version préliminaire) du service Azure Machine Learning. 

Chaque module représente un ensemble de code qui peut s’exécuter indépendamment et effectuer un tâche de machine learning, étant donné les entrées requises. Un module peut contenir un algorithme particulier, ou effectuer une tâche qui est importante dans l’apprentissage automatique, telles que le remplacement de valeur manquant, ou l’analyse statistique. 

> [!TIP]
> Dans n’importe quelle expérience dans l’interface visuelle, vous pouvez obtenir des informations sur un module spécifique. Sélectionnez le module, puis sélectionnez le **aide plus** lien dans le **aide rapide** volet.

Les modules sont organisés par fonctionnalités :

**Conversions de format de données**

  + [Convertir en fichier CSV ](convert-to-csv.md)

**Données d’entrée et sortie modules** faire le travail de déplacement des données à partir de sources cloud dans votre expérience. Vous pouvez écrire vos résultats ou des données intermédiaires à stockage Azure, une base de données SQL ou Hive, lors de l’exécution d’une expérience ou utiliser le stockage cloud d’échanger des données entre les expériences.  

  + [Importer des données](import-data.md)

  + [Exporter les données](export-data.md)

  + [Entrer manuellement les données](enter-data-manually.md)


**Modules de transformation de données** prennent en charge les opérations sur les données qui sont uniques à l’apprentissage automatique, telles que la normalisation ou le compartimentage de données, la sélection de fonctionnalité et la réduction de dimensionnalité.

  + [Sélectionner des colonnes dans le jeu de données](select-columns-in-dataset.md)

  + [Modifier les métadonnées](edit-metadata.md)

  + [Nettoyage des données manquantes](clean-missing-data.md)

  + [Ajouter des colonnes](add-columns.md)

  + [Ajouter des lignes](add-rows.md)

  + [Supprimer les lignes en double](remove-duplicate-rows.md)

  + [Fractionner les données](split-data.md)

  + [Normaliser les données](normalize-data.md)

  + [Partition and Sample](partition-and-sample.md)


**Algorithmes d’apprentissage** telles que le clustering, machine à vecteurs de support ou des réseaux neuronaux, sont disponibles au sein des modules individuels qui vous permettent de personnaliser la tâche d’apprentissage automatique avec les paramètres appropriés.  
  + [Noter le modèle](score-model.md)

  + [Affecter des données aux Clusters ](assign-data-to-clusters.md)

  + [Former le modèle](train-model.md)

  + [Former le modèle de Clustering](train-clustering-model.md)

  + [Évaluer le modèle](evaluate-model.md)

  + [Appliquer une Transformation](apply-transformation.md)

  + [Régression linéaire](linear-regression.md)

  + [Régression de réseau neuronal](neural-network-regression.md)

  + [Régression de forêts de décision](decision-forest-regression.md)

  + [Les arbres de décision Tree Regression](boosted-decision-tree-regression.md)

  + [Arbre de décision optimisé à deux classes](two-class-boosted-decision-tree.md)

  + [Two-Class Logistic Regression](two-class-logistic-regression.md)

  + [Régression logistique multiclasse](multiclass-logistic-regression.md)

  + [Réseau neuronal multiclasse](multiclass-neural-network.md)

  + [Forêt de décision multiclasse](multiclass-decision-forest.md)

  + [Perceptron Moyenné Two-Class](two-class-averaged-perceptron.md)

  + [Two-Class Decision Forest](two-class-decision-forest.md)

  + [Two-Class Neural Network](two-class-neural-network.md)

  + [Two-Class Support Vector Machine](two-class-support-vector-machine.md)
  
  + [Clustering K-Means](k-means-clustering.md)


**Module Python** facilite l’exécution d’une fonction personnalisée. Vous écrivez le code et l’incorporer dans un module, à intégrer Python avec un service de l’expérience.
  + [Exécutez le Script Python](execute-python-script.md)

  + [Créer le modèle Python](create-python-model.md)


