---
title: Informations de référence sur les algorithmes et les modules
description: En savoir plus sur les modules disponibles dans le concepteur Azure Machine Learning (préversion)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: df2e73138cec6c684d612543280871eb2d49b207
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456355"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Informations de référence sur les algorithmes et les modules du concepteur Azure Machine Learning

Ce contenu de référence présente l’arrière-plan technique de chacun des algorithmes d’apprentissage automatique et les modules disponibles dans le concepteur Azure Machine Learning (préversion).

Chaque module représente un ensemble de code qui peut s’exécuter indépendamment et effectuer une tâche de Machine Learning en fonction des entrées requises. Un module peut contenir un algorithme particulier ou effectuer une tâche de Machine Learning importante comme le remplacement de valeur manquante ou l’analyse statistique.

> [!TIP]
> Quel que soit le pipeline dans le concepteur, vous pouvez obtenir des informations sur un module spécifique. Sélectionnez le module, puis sélectionnez le lien **Plus d’aide** dans le volet **Aide rapide**.

## <a name="modules"></a>Modules

Les modules sont organisés par fonctionnalités :

| Fonctionnalités | Description | Module |
| --- |--- | --- |
|  | **Préparation des données** : | |
| Entrée et sortie de données | Déplacez des données de sources cloud dans votre pipeline. Écrivez vos résultats ou des données intermédiaires dans Stockage Azure, une base de données SQL, ou Hive, lors de l’exécution d’un pipeline, ou utilisez le stockage cloud pour échanger des données entre des pipelines.  | [Entrer des données manuellement](enter-data-manually.md) <br/> [Exporter les données](export-data.md) <br/> [Importer des données](import-data.md) |
| Transformation des données | Opérations sur les données qui sont uniques à Machine Learning, telles que la normalisation ou le compartimentage de données, la réduction de dimensionnalité et la conversion de données entre différents formats de fichier.| [Ajouter des colonnes](add-columns.md) <br/> [Ajouter des lignes](add-rows.md) <br/> [Appliquer une opération mathématique](apply-math-operation.md) <br/> [Appliquer une transformation SQL](apply-sql-transformation.md) <br/> [Nettoyer les données manquantes](clean-missing-data.md) <br/> [Détourer les valeurs](clip-values.md) <br/> [Convertir au format CSV](convert-to-csv.md) <br/> [Convertir en jeu de données](convert-to-dataset.md) <br/> [Modifier des métadonnées](edit-metadata.md) <br/> [Joindre des données](join-data.md) <br/> [Normaliser les données](normalize-data.md) <br/> [Partition et échantillon](partition-and-sample.md)  <br/> [Supprimer les données en double](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Sélectionner une transformation de colonnes](select-columns-transform.md) <br/> [Sélectionner des colonnes dans le jeu de données](select-columns-in-dataset.md) <br/> [Fractionner les données](split-data.md) |
| Sélection de caractéristiques | Sélectionnez un sous-ensemble de fonctionnalités pertinentes et utiles à utiliser dans la création d’un modèle analytique. | [Sélection de caractéristiques par filtrage](filter-based-feature-selection.md) <br/> [Importance de la fonctionnalité de permutation](permutation-feature-importance.md) |
| Fonctions statistiques | Fournissez un large éventail de méthodes statistiques relatives à la science des données. | [Résumer les données](summarize-data.md)|
|  | **Algorithmes de Machine Learning** : | |
| régression ; | Prédisez une valeur. | [Régression d’arbre de décision boosté](boosted-decision-tree-regression.md) <br/> [Régression de forêt d’arbres décisionnels](decision-forest-regression.md) <br/> [Régression linéaire](linear-regression.md)  <br/> [Régression de réseau neuronal](neural-network-regression.md)  <br/> |
| Clustering | Regroupez des données.| [Clustering k-moyennes](k-means-clustering.md)
| classification ; | Prédisez une classe.  Choisissez entre des algorithmes binaires (à deux classes) ou multiclasses.| [Arbre de décision multiclasse optimisé](multiclass-boosted-decision-tree.md) <br/> [Forêt d’arbres décisionnels multiclasse](multiclass-decision-forest.md) <br/> [Régression logistique multiclasse](multiclass-logistic-regression.md)  <br/> [Réseau neuronal multiclasse](multiclass-neural-network.md) <br/> [Une ou toutes les multiclasses](one-vs-all-multiclass.md) <br/> [Perceptron moyenné à deux classes](two-class-averaged-perceptron.md) <br/>  [Arbre de décision optimisé à deux classes](two-class-boosted-decision-tree.md)  <br/> [Forêt d’arbres décisionnels à deux classes](two-class-decision-forest.md) <br/>  [Régression logistique à deux classes](two-class-logistic-regression.md) <br/> [Réseau neuronal à deux classes](two-class-neural-network.md) <br/> [Machine à vecteurs de support à deux classes](two-class-support-vector-machine.md) | 
|  | **Créer et évaluer des modèles** : | |
| Apprentissage du modèle | Exécutez des données au moyen de l’algorithme. |  [Entraîner un modèle de clustering](train-clustering-model.md) <br/> [Former le modèle](train-model.md)  <br/> [Optimiser les hyperparamètres du modèle](tune-model-hyperparameters.md) |
| Scoring et évaluation d’un modèle | Mesurez la précision du modèle entraîné. | [Appliquer une transformation](apply-transformation.md) <br/> [Attribuer des données à des clusters](assign-data-to-clusters.md) <br/> [Modèle de validation croisée](cross-validate-model.md) <br/> [Évaluer le modèle](evaluate-model.md) <br/> [Noter le modèle](score-model.md) |
| Langage Python | Écrivez du code et incorporez-le dans un module pour intégrer Python à votre pipeline. | [Créer un modèle Python](create-python-model.md) <br/> [Exécuter un script Python](execute-python-script.md) |
| Langage R | Écrivez du code et incorporez-le dans un module pour intégrer R à votre pipeline. | [Exécuter un script R](execute-r-script.md) |
| Analyse de texte | Fournissez des outils de calcul spécialisés pour travailler avec du texte structuré et non structuré. | [Extraire les caractéristiques de n-grammes du texte](extract-n-gram-features-from-text.md) <br/> [Hachage des caractéristiques](feature-hashing.md) <br/> [Pré-traiter le texte](preprocess-text.md) |
| Recommandation | Génération de modèles de recommandation. | [Évaluer le générateur de recommandations](evaluate-recommender.md) <br/> [Noter le générateur de recommandations SVD](score-svd-recommender.md) <br/> [Entraîner le générateur de recommandations SVD](train-SVD-recommender.md) |

## <a name="error-messages"></a>Messages d’erreur

Découvrez les [messages d’erreur et codes d’exception](machine-learning-module-error-codes.md) que vous pourriez rencontrer en utilisant des modules dans le concepteur Azure Machine Learning.
