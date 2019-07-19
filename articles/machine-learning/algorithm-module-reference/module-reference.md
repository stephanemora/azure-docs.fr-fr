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
ms.openlocfilehash: e73d4ebd3eb05f7cf217573d8112e3dbbe6d3a37
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514079"
---
# <a name="algorithm--module-reference-overview"></a>Vue d’ensemble des informations de référence sur les algorithmes et les modules

Ce contenu de référence présente l’arrière-plan technique de chacun des algorithmes de Machine Learning et les modules disponibles dans l’interface visuelle (préversion) de Machine Learning service.

Chaque module représente un ensemble de code qui peut s’exécuter indépendamment et effectuer une tâche de Machine Learning en fonction des entrées requises. Un module peut contenir un algorithme particulier ou effectuer une tâche de Machine Learning importante comme le remplacement de valeur manquante ou l’analyse statistique.

> [!TIP]
> Quelle que soit l’expérience dans l’interface visuelle, vous pouvez obtenir des informations sur un module spécifique. Sélectionnez le module, puis sélectionnez le lien **Plus d’aide** dans le volet **Aide rapide**.

## <a name="modules"></a>Modules

Les modules sont organisés par fonctionnalités :

| Fonctionnalités | Description | Module |
| --- |--- | ---- |
| Conversion des formats de données | Convertissez des données entre différents formats de fichier utilisés dans Machine Learning, | [Convertir au format CSV](convert-to-csv.md) |
| Entrée et sortie de données | Déplacez des données de sources cloud dans votre expérience. Écrivez vos résultats ou des données intermédiaires dans Stockage Azure, une base de données SQL, ou Hive, lors de l’exécution d’une expérience, ou utilisez le stockage cloud pour échanger des données entre des expériences.  | [Importer des données](import-data.md)<br/>[Exporter les données](export-data.md)<br/>[Entrer des données manuellement](enter-data-manually.md) |
| Transformation des données | Opérations sur les données qui sont uniques à Machine Learning, telles que la normalisation ou le compartimentage de données, la sélection de fonctionnalité et la réduction de dimensionnalité.| [Sélectionner des colonnes dans le jeu de données](select-columns-in-dataset.md) <br/> [Modifier des métadonnées](edit-metadata.md) <br/> [Nettoyage des données manquantes](clean-missing-data.md) <br/> [Ajouter des colonnes](add-columns.md) <br/> [Ajouter des lignes](add-rows.md) <br/> [Supprimer les données en double](remove-duplicate-rows.md) <br/> [Joindre des données](join-data.md) <br/> [Fractionner les données](split-data.md) <br/> [Normaliser des données](normalize-data.md) <br/> [Partition et échantillon](partition-and-sample.md) |
| Modules Python et R | Écrivez du code et incorporez-le dans un module pour intégrer Python et R à votre expérience. | [Exécuter un script Python](execute-python-script.md)   <br/> [Créer un modèle Python](create-python-model.md) <br/> [Exécuter un script R](execute-r-script.md)
|  | **Algorithmes de Machine Learning** : | |
| classification ; | Prédisez une classe.  Choisissez entre des algorithmes binaires (à deux classes) ou multiclasses.| [Forêt d’arbres décisionnels multiclasse](multiclass-decision-forest.md) <br/> [Régression logistique multiclasse](multiclass-logistic-regression.md)  <br/> [Réseau neuronal multiclasse](multiclass-neural-network.md)  <br/>  [Régression logistique à deux classes](two-class-logistic-regression.md)  <br/>[Perceptron moyenné à deux classes](two-class-averaged-perceptron.md) <br/> [Arbre de décision&nbsp;mis en avant&nbsp;à deux&nbsp;classes](two-class-boosted-decision-tree.md)  <br/> [Forêt d’arbres décisionnels à deux classes](two-class-decision-forest.md)  <br/> [Réseau neuronal à deux classes](two-class-neural-network.md)  <br/> [Prise en charge&nbsp;de machine&nbsp;vectorielle&nbsp;à deux classes](two-class-support-vector-machine.md) 
| Clustering | Regroupez des données.| [Clustering k-moyennes](k-means-clustering.md)
| régression ; | Prédisez une valeur. | [Régression linéaire](linear-regression.md)  <br/> [Régression de réseau neuronal](neural-network-regression.md)  <br/> [Régression de forêt d’arbres décisionnels](decision-forest-regression.md)  <br/> [Régression&nbsp;d’arbre&nbsp;de décision&nbsp;mise en avant](boosted-decision-tree-regression.md)
|  | **Créer et évaluer des modèles** : | |
| Former   | Exécutez des données au moyen de l’algorithme. | [Entraîner le modèle](train-model.md)  <br/> [Entraîner un modèle de clustering](train-clustering-model.md)    |
| Évaluer le modèle | Mesurez la précision du modèle entraîné. |  [Évaluer le modèle](evaluate-model.md)
| Score | Obtenez des prédictions du modèle que vous venez d’entraîner. | [Appliquer une transformation](apply-transformation.md)<br/>[Affecter&nbsp;des données&nbsp;à des&nbsp;clusters](assign-data-to-clusters.md) <br/>[Noter un modèle](score-model.md)

## <a name="error-messages"></a>messages d'erreur

Découvrez les [messages d’erreur et codes d’exception](machine-learning-module-error-codes.md) que vous pouvez rencontrer en utilisant des modules dans l’interface visuelle d’Azure Machine Learning service.
