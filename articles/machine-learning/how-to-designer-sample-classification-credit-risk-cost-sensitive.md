---
title: 'Concepteur : Prédire le risque de crédit (exemple)'
titleSuffix: Azure Machine Learning
description: Créez un classifieur et utilisez des scripts Python personnalisés pour prédire le risque de crédit à l’aide du concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 54ca0df005dccceacc88044a51f31ad784b7071b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763400"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Créer un classifieur et utiliser des scripts Python pour prédire le risque de crédit à l’aide du concepteur Azure Machine Learning

**Exemple 4 du concepteur (préversion)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Cet article vous montre comment générer un pipeline de Machine Learning complexe à l’aide du concepteur (préversion). Vous allez apprendre à implémenter une logique personnalisée à l’aide de scripts Python et à comparer plusieurs modèles, afin de choisir la meilleure option.

Cet exemple forme un classifieur à la prédiction des risques de crédit à l’aide d’informations sur les demandes de crédit (historique de crédit, âge et nombre de cartes de crédit, par exemple). Toutefois, vous pouvez appliquer les concepts de cet article pour gérer les difficultés liées à l’apprentissage automatique.

Voici le graphique complet associé à ce pipeline :

[![Graphique du pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Cliquez sur l’exemple 4 pour l’ouvrir.

## <a name="data"></a>Données

Cet exemple utilise le jeu de données German Credit Card (Carte de crédit allemande) issu du référentiel UC Irvine. Il contient 1 000 exemples intégrant 20 caractéristiques et une étiquette. Chaque exemple représente une personne. Les 20 fonctionnalités incluent des caractéristiques numériques et classées par catégorie. Pour en savoir plus sur le jeu de données, consultez le [site web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). La dernière colonne est l’étiquette, qui désigne le risque de crédit et a uniquement deux valeurs possibles : risque de crédit élevé = 2 et risque de crédit faible = 1.

## <a name="pipeline-summary"></a>Résumé du pipeline

Dans ce pipeline, vous comparez deux approches différentes permettant de générer des modèles afin de résoudre ce problème :

- Formation avec le jeu de données d’origine.
- Formation avec un jeu de données répliqué.

En utilisant les deux approches, vous évaluez les modèles à l’aide d’un jeu de données de test avec la réplication, afin de vérifier que les résultats sont alignés avec la fonction de coût. Testez deux classifieurs selon deux approches : **Machine à vecteurs de support à deux classes** et **Arbre de décision optimisé à deux classes**.

Le coût d’une erreur de classification d’un risque de crédit faible en risque élevé est de 1, alors qu’il est de 5 dans le cas inverse (classification d’un risque de crédit élevé en risque faible). Nous utilisons un module **Exécuter un script Python** pour prendre en compte le coût de cette classification incorrecte.

Voici le graphique du pipeline :

[![Graphique du pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Traitement des données

Commencez par utiliser le module **Éditeur de métadonnées** pour ajouter des noms de colonne, afin de remplacer les noms par défaut par des noms plus explicites, obtenus à partir de la description du jeu de données sur le site UCI. Entrez les nouveaux noms de colonnes sous la forme de valeurs séparées par des virgules dans le champ **Nouvelle colonne** de **l’éditeur de métadonnées**.

Ensuite, générez les ensembles d’entraînement et de test utilisés pour développer le modèle de prédiction des risques. Divisez le jeu de données d’origine en ensembles d’entraînement et de test de même taille, à l’aide du module **Fractionner les données**. Pour créer des jeux de taille égale, définissez l’option **Fraction de lignes dans le premier jeu de données de sortie** sur 0.7.

### <a name="generate-the-new-dataset"></a>Générer le nouveau jeu de données

Le coût d’une sous-évaluation des risques étant élevé, définissez le coût d’une classification incorrecte comme suit :

- Pour les cas à haut risque considérés à tort comme à faible risque : 5
- Pour les cas à faible risque considérés à tort comme à risque élevé : 1

Pour refléter cette fonction de coût, générez un nouveau jeu de données. Dans le nouveau jeu de données, chaque exemple à haut risque est répliqué cinq fois, alors que le nombre d’exemples à faible risque ne change pas. Divisez les données en ensembles de données d’entraînement et de test avant la réplication, afin d’éviter qu’une même ligne figure dans les deux ensembles.

Pour répliquer les données à haut risque, placez ce code Python dans un module **Exécuter un script Python** :

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Le module **Exécuter un script Python** réplique les ensembles de données de formation et de test.

### <a name="feature-engineering"></a>Ingénierie des caractéristiques

L’algorithme **Machine à vecteurs de support à deux classes** requiert des données normalisées. Vous devez donc utiliser le module **Normaliser les données** pour normaliser les plages de fonctionnalités numériques avec une transformation `tanh`. Une transformation `tanh` convertit toutes les fonctionnalités numériques en valeurs sur une plage de 0 à 1, tout en préservant la distribution générale de ces valeurs.

Le module **Machine à vecteurs de support à deux classes** gère les caractéristiques de chaîne, en les convertissant en caractéristiques classées par catégorie, puis en caractéristiques binaires, avec une valeur de 0 ou 1. Par conséquent, vous n’avez pas besoin de normaliser ces fonctionnalités.

## <a name="models"></a>Modèles

Étant donné que vous avez appliqué deux classifieurs, **Machine à vecteurs de support à deux classes** et **Arbre de décision optimisé à deux classes**, ainsi que deux jeux de données, vous générez quatre modèles au total :

- Module Machine à vecteurs de support à deux classes formé à l’aide des données d’origine.
- Module Machine à vecteurs de support à deux classes formé à l’aide des données répliquées.
- Module Arbre de décision optimisé à deux classes formé avec les données d’origine.
- Module Arbre de décision optimisé à deux classes formé avec les données répliquées.

Cet exemple utilise le workflow de science des données standard pour créer, entraîner et tester les modèles :

1. Initialisez les algorithmes d’apprentissage à l’aide des modules **Machine à vecteurs de support à deux classes** et **Arbre de décision optimisé à deux classes**.
1. Utilisez l’option **Entraîner le modèle** pour appliquer l’algorithme aux données et créer le modèle réel.
1. Utilisez l’option **Noter un modèle** pour créer des notes en utilisant les exemples de test.

Le diagramme suivant montre une partie de ce pipeline, dans lequel les ensembles de formation d’origine et répliqués sont utilisés pour effectuer l'apprentissage de deux modèles SMV différents. La fonction **Entraîner le modèle** est connectée à l’ensemble de formation, alors que la fonction **Noter un modèle** est connectée à l’ensemble de test.

![Graphique de pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

Au cours de l’étape d’évaluation du pipeline, vous calculez la précision de chacun des quatre modèles. Pour ce pipeline, utilisez **Évaluer le modèle** pour comparer les exemples présentant le même coût de classification incorrecte.

Le module **Évaluer le modèle** peut calculer les mesures de performances pour un maximum de deux modèles notés. Vous utilisez une seule instance du module **Évaluer le modèle** pour évaluer les deux modèles Machine à vecteurs de support à deux classes, et une autre instance du module **Évaluer le modèle** pour évaluer les deux modèles Arbre de décision optimisé à deux classes.

Notez que le jeu de données de test répliqué est utilisé en tant qu’entrée pour **noter le modèle**. En d’autres termes, les scores de précision finale incluent le coût lié à un étiquetage incorrect.

## <a name="combine-multiple-results"></a>Combiner plusieurs résultats

Le module **Évaluer le modèle** génère une table avec une seule ligne, qui contient différentes mesures. Pour créer un seul ensemble de résultats de précision, nous utilisons tout d’abord le module **Ajouter des lignes**, afin de combiner les résultats dans une table unique. Nous utilisons ensuite le script Python suivant dans le module **Exécuter un script Python**, afin d’ajouter le nom du modèle et l’approche de formation pour chaque ligne dans la table de résultats :

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Résultats

Pour afficher les résultats du pipeline, vous pouvez cliquer avec le bouton de droite sur la sortie Visualiser du dernier module **Sélectionner des colonnes dans le jeu de données**.

![Visualiser la sortie](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

La première colonne indique l’algorithme d’apprentissage automatique utilisé pour générer le modèle.

La deuxième colonne inclut le type de l’ensemble de formation.

La troisième colonne contient la valeur de précision, sensible au coût.

À partir de ces résultats, vous pouvez voir que c’est le modèle créé avec le module **Machine à vecteurs de support à deux classes** et formé sur l’ensemble de données de formation répliqué qui offre le plus haut degré de précision.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres échantillons disponibles pour le concepteur :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification avec sélection des caractéristiques : Prédiction des revenus](how-to-designer-sample-classification-predict-income.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-designer-sample-classification-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-designer-sample-classification-flight-delay.md)
- [Exemple 7 - Classification de texte : Jeu de données Wikipédia SP 500](how-to-designer-sample-text-classification.md)
