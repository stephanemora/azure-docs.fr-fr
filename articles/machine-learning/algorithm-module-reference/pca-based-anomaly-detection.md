---
title: 'Détection d’anomalie reposant sur l’ACP : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Détection d’anomalie reposant sur l’ACP pour créer un modèle de détection d’anomalie basé sur l’analyse en composantes principales (ACP).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79504077"
---
# <a name="pca-based-anomaly-detection"></a>Détection des anomalies reposant sur le PCA

Cet article explique comment utiliser le module **Détection d’anomalie reposant sur l’ACP** dans le concepteur Azure Machine Learning (préversion) pour créer un modèle de détection d’anomalie basé sur l’analyse en composantes principales (ACP).

Ce module vous aide à créer un modèle dans des scénarios où il est facile d’obtenir des données de formation à partir d’une classe, telles que des transactions valides, mais difficile d’obtenir suffisamment d’exemples d’anomalies ciblées. 

Par exemple, pour détecter les transactions frauduleuses, très souvent, vous n’avez pas suffisamment d’exemples de fraude pour effectuer l’apprentissage du modèle, alors que vous avez de nombreux exemples de transactions correctes. Le module **Détection d’anomalie reposant sur l’ACP** résout le problème en analysant les fonctionnalités disponibles pour déterminer ce qui constitue une classe « normale » et en appliquant des mesures de distance pour identifier les cas qui constituent des anomalies. Cela vous permet d’entraîner un modèle à l’aide de données déséquilibrées existantes.

## <a name="more-about-principal-component-analysis"></a>En savoir plus sur l’analyse en composantes principales

L’*analyse en composantes principales*, souvent abrégée ACP, est une technique établie dans le domaine du Machine Learning. Cette technique est fréquemment utilisée dans l’analyse exploratoire des données, car elle révèle la structure interne des données et explique la variance dans les données.

L’ACP fonctionne en analysant les données qui contiennent plusieurs variables. Elle recherche les corrélations entre les variables et détermine la combinaison des valeurs qui capturent le mieux les différences dans les résultats. Ces valeurs de fonctionnalités combinées sont utilisées pour créer un espace de fonctionnalités plus compact appelé les *composantes principales*.

Pour la détection d’anomalie, chaque nouvelle entrée est analysée et l’algorithme de détection d’anomalies calcule sa projection sur les vecteurs propres, ainsi qu’une erreur de reconstruction normalisée. L’erreur normalisée est utilisée comme score d’anomalie. Plus l’erreur est élevée, plus l’instance est anormale.

Pour plus d’informations sur le fonctionnement de l’ACP et sur l’implémentation de la détection d’anomalie, consultez ces articles :

- [A randomized algorithm for principal component analysis](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan et Tygert

- [Finding Structure with Randomness: Probabilistic Algorithms for Constructing Approximate Matrix Decompositions](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (Téléchargement PDF). Halko, Martinsson et Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Comment configurer la détection d’anomalie ACP

1. Ajoutez le module **Détection d’anomalie reposant sur l’ACP** à votre pipeline dans le concepteur. Ce module figure dans la catégorie **Détection d’anomalie**.

2. Dans le volet droit du module **Détection d’anomalie reposant sur l’ACP**, cliquez sur l’option **Mode d’entraînement** et indiquez si vous souhaitez entraîner le modèle à l’aide d’un ensemble spécifique de paramètres ou utiliser un balayage des paramètres pour rechercher les meilleurs paramètres.

    - **Single Parameter** (Paramètre unique) :  sélectionnez cette option si vous savez comment vous voulez configurer le modèle, et fournissez un ensemble spécifique de valeurs en tant qu’arguments.

3. **Nombre de composantes à utiliser dans l’ACP** : Spécifiez le nombre de fonctionnalités de sortie ou de composantes que vous souhaitez générer.

    Le choix du nombre de composantes à inclure est une partie importante de la conception d’expérimentation à l’aide de l’ACP. D’une manière générale, vous ne devez pas inclure le même nombre de composantes ACP que de variables. Au lieu de cela, vous devez commencer avec un plus petit nombre de composantes et les augmenter jusqu’à ce que certains critères soient remplis.

    Les meilleurs résultats sont obtenus lorsque le nombre de composantes de sortie est **inférieur** au nombre de colonnes de fonctionnalités disponibles dans le jeu de données.

4. Spécifiez le nombre de suréchantillonnages à effectuer lors de la formation de l’ACP aléatoire. Dans les problèmes de détection d’anomalie, les données déséquilibrées compliquent l’application des techniques ACP standard. En spécifiant une quantité de suréchantillonnage, vous pouvez augmenter le nombre d’instances cibles.

    Si vous spécifiez 1, aucun suréchantillonnage n’est effectué. Si vous spécifiez une valeur supérieure à 1, des échantillons supplémentaires sont générés pour être utilisés dans la formation du modèle.

    Il existe deux options, selon que vous utilisez ou non un balayage des paramètres :

    - **Paramètre de suréchantillonnage pour l’ACP aléatoire** : Saisissez un nombre entier unique qui représente le rapport entre le suréchantillonnage de la classe minoritaire et la classe normale. (Disponible lors de l’utilisation de la méthode de formation **Paramètre unique**.)

    > [!NOTE]
    > Vous ne pouvez pas afficher le jeu de données suréchantillonné. Pour plus d’informations sur l’utilisation du suréchantillonnage avec l’ACP, consultez [Notes techniques](#technical-notes).

5. **Activer la normalisation des moyennes des fonctionnalités d’entrée** :   Sélectionnez cette option pour normaliser toutes les fonctionnalités d’entrée à une moyenne de zéro. La normalisation ou la mise à l’échelle sur zéro est généralement recommandée pour l’ACP, car l’objectif de cette dernière est d’optimiser l’écart entre les variables.

     Cette option est activée par défaut. Désélectionnez cette option si les valeurs ont déjà été normalisées à l’aide d’une méthode ou mise à l’échelle différente.

6. Connectez un jeu de données d’apprentissage balisé à l’un des modules de formation :

    - Si vous définissez l’option **Créer un mode d’apprentissage** sur **Paramètre unique**, utilisez le module [Entraîner le modèle de détection d’anomalie](train-anomaly-detection-model.md).

7. Envoyez le pipeline.

## <a name="results"></a>Résultats

Une fois la formation terminée, vous pouvez enregistrer le modèle formé ou le connecter au module [Noter le modèle](score-model.md) pour prédire les scores d’anomalie.

Pour évaluer les résultats d’un modèle de détection d’anomalie, vous devez effectuer quelques étapes supplémentaires :

1. Vérifier qu’une colonne de score est disponible dans les deux jeux de données

    Si vous essayez d’évaluer un modèle de détection d’anomalie et que vous obtenez l’erreur « Il n’y a pas de colonne de score dans le jeu de données noté à comparer », cela signifie que vous utilisez un jeu de données d’évaluation classique qui contient une colonne d’étiquette, mais pas de score de probabilité. Vous devez choisir un jeu de données qui correspond à la sortie de schéma pour les modèles de détection d’anomalie, qui comprend une colonne **Étiquettes notées** et **Probabilités notées**.

2. S’assurer que les colonnes d’étiquette sont marquées

    Parfois, les métadonnées associées à la colonne d’étiquette sont supprimées dans le graphique de pipeline. Dans ce cas, lorsque vous utilisez le module [Évaluer le modèle](evaluate-model.md) pour comparer les résultats de deux modèles de détection d’anomalie, vous pouvez obtenir l’erreur « Aucune colonne d’étiquette dans le jeu de données noté » ou « Il n’y a pas de colonne d’étiquette dans le jeu de données noté à comparer ».

    Vous pouvez éviter cette erreur en ajoutant le module [Modifier les métadonnées](edit-metadata.md) avant le module [Évaluer le modèle](evaluate-model.md). Utilisez le sélecteur de colonne pour choisir la colonne de classe, puis dans la liste déroulante **Champs**, sélectionnez **Étiquette**.

3. Utilisez [Exécuter le script Python](execute-python-script.md) pour ajuster les catégories de colonne d’étiquette à 1(positive, normal) et 0(negative, abnormal).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Notes techniques

Cet algorithme utilise l’ACP pour se rapprocher du sous-espace contenant la classe normale. Le sous-espace est fractionné par vecteurs propres associés au valeurs propres supérieures de la matrice de covariance des données. Pour chaque nouvelle entrée, le détecteur d’anomalies calcule d’abord sa projection sur les vecteurs propres avant de calculer l’erreur de reconstruction normalisée. Cette erreur représente le score d’anomalie. Plus l’erreur est élevée, plus l’instance est anormale. Pour plus d’informations sur la façon dont l’espace normal est calculé, consultez la page Wikipédia : [Analyse en composantes principales](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 

Pour obtenir la liste des erreurs spécifiques aux modules du concepteur, consultez [Exceptions et codes d’erreur du concepteur (préversion)](designer-error-codes.md).