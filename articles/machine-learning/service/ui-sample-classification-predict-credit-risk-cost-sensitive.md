---
title: 'Classification : Prédire le risque de crédit (coût sensible)'
titleSuffix: Azure Machine Learning service
description: Cet exemple d’expérience interface visuelle montre comment utiliser un script Python personnalisé pour effectuer la classification binaire sensible de coût. Il prévoit de risque de crédit en fonction des informations fournies dans une application de crédit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 433c258f86705f66e0163100407be7996d68bc6b
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65440960"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Exemple 4 : Classification : Prédire le risque de crédit (coût sensible)

Cet exemple d’expérience interface visuelle montre comment utiliser un script Python personnalisé pour effectuer la classification binaire sensible de coût. Le coût de classer les échantillons positifs est cinq fois le coût de la classification des échantillons négatifs.

Cet exemple prédit risque de crédit basée sur les informations fournies dans une application de crédit, en prenant en compte les coûts de classification incorrecte.

Dans cette expérience, nous comparons les deux approches différentes pour générer des modèles pour résoudre ce problème :

- Formation avec le jeu de données d’origine.
- Formation avec un jeu de données répliquée.

Avec les deux approches, nous évaluons les modèles à l’aide du jeu de données de test avec la réplication pour vous assurer que les résultats sont alignées avec la fonction de coût. Nous testons deux classifieurs avec les deux approches : **Two-Class Support Vector Machine** et **Two-Class arbre de décision**.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le **Open** bouton de l’expérience de l’exemple 4 :

    ![Ouvrez l’expérience](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="related-sample"></a>Exemple

Consultez [exemple 3 - Classification : Prédiction du risque (Basic) de crédit](ui-sample-classification-predict-churn.md) pour une expérience simple qui résout le problème même en tant que cette expérience, sans ajustement des coûts de classification incorrecte.

## <a name="data"></a>Données

Nous utilisons le jeu de données de carte de crédit allemande à partir du référentiel de UC Irvine. Ce jeu de données contient des exemples de 1 000 avec 20 caractéristiques et 1 étiquette. Chaque exemple représente une personne. Les fonctionnalités de 20 incluent des caractéristiques numériques et catégorielles. Consultez le [site Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) pour plus d’informations sur le jeu de données. La dernière colonne est l’étiquette, qui désigne le risque de crédit et a uniquement deux valeurs possibles : risque de crédit élevé = 2 et risque de crédit faible = 1.

## <a name="experiment-summary"></a>Résumé de l’expérience

Le coût de la classification d’un exemple à faible risque aussi élevé est 1, et le coût de la classification d’un exemple à haut risque faibles est 5. Nous utilisons un **exécuter le Script Python** module pour prendre en compte cette classification incorrecte des coûts.

Voici le graphique de l’expérience :

[![Graphique de l’expérience](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Traitement des données

Nous commençons à l’aide de la **Metadata Editor** module pour ajouter des noms de colonne pour remplacer les noms de colonne par défaut avec des noms plus explicites, obtenu à partir de la description du jeu de données sur le site UCI. Nous fournissons les nouveaux noms de colonne en tant que valeurs séparées par des virgules dans le **nouvelle colonne** champ nom de la **Metadata Editor**.

Ensuite, nous générer la formation jeux et de test utilisé pour développer le modèle prédictif de risque. Nous divisons le jeu de données d’origine en jeux d’apprentissage et de test de la même taille à l’aide de la **fractionner les données** module. Pour créer des ensembles de taille égale, nous définissons le **Fraction de lignes dans le premier jeu de données de sortie** option 0,5.

### <a name="generate-the-new-dataset"></a>Générer le nouveau jeu de données

Étant donné que le coût de surestimer le risque est élevé, nous définissons le coût de classification incorrecte comme suit :

- Pour les cas à haut risque de mal classées comme étant à faible risque : 5.
- Pour les cas de faible risque classé par erreur comme un risque élevé : 1

Pour refléter cette fonction de coût, nous générons un nouveau jeu de données. Dans le nouveau jeu de données, chaque exemple à haut risque est répliqué cinq fois, mais le nombre d’exemples de faible risque ne change pas. Nous fractionner les données en jeux de données apprentissage et de test avant la réplication pour empêcher la même ligne dans les deux ensembles.

Pour répliquer les données à haut risque, nous avons placé ce code Python dans un **exécuter le Script Python** module :

```
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Le **exécuter le Script Python** module réplique la formation et le test des jeux de données.

### <a name="feature-engineering"></a>Ingénierie des caractéristiques

Le **Two-Class Support Vector Machine** algorithme requiert des données normalisées. Nous utilisons le **normaliser les données** module pour normaliser les plages de toutes les fonctionnalités numériques avec un `tanh` transformation. Un `tanh` transformation convertit les valeurs dans une plage de 0 et 1 toutes les fonctionnalités numériques tout en conservant la distribution globale des valeurs.

Le **Two-Class Support Vector Machine** module gère les fonctions de chaîne, en les convertissant en fonctionnalités catégorielles et puis à binaire avec la valeur 0 ou 1. Par conséquent, nous n’avez pas besoin de normaliser ces fonctionnalités.

## <a name="models"></a>Modèles

Étant donné que nous appliquons deux classifieurs, **Two-Class Support Vector Machine** (SVM) et **Two-Class Boosted Decision Tree**et également utiliser deux jeux de données, nous générons un total de quatre modèles :

- SVM formé avec les données d’origine.
- SVM formé avec les données répliquées.
- Arbre de décision augmentés formé avec les données d’origine.
- Arbre de décision augmentés formés avec des données répliquées.

Nous utilisons le workflow expérimental standard pour créer, former et tester les modèles :

1. Initialiser les algorithmes d’apprentissage automatique, à l’aide de **Two-Class Support Vector Machine** et **Two-Class Boosted Decision Tree**.
1. Utilisez **former le modèle** pour appliquer l’algorithme aux données et créer le modèle réel.
3. Utilisez **noter le modèle** pour produire des scores en utilisant les exemples de test.

Le diagramme suivant montre une partie de cette expérience, dans lequel les jeux d’apprentissage d’origine et répliquées sont utilisés pour l’apprentissage de deux modèles SVM. **Former modèle** est connecté au jeu d’apprentissage, et **noter le modèle** est connecté à l’ensemble de test.

![Graphique d’expérience](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)


Dans l’étape d’évaluation de l’expérience, nous calculons la précision de chacun des quatre modèles. Pour cette expérience, nous utilisons **évaluer le modèle** pour comparer des exemples associés à la classification incorrecte de même coût.

Le **évaluer le modèle** module peut calculer les mesures de performances pour un maximum de deux modèles notés. Nous utilisons une seule instance de **évaluer le modèle** pour évaluer les deux modèles SVM et une autre instance de **évaluer le modèle** pour évaluer les deux modèles d’arbre de décision optimisé.

Notez que le jeu de données répliquées de test est utilisé comme entrée pour **noter le modèle**. En d’autres termes, les scores de précision finale incluent le coût pour obtenir les étiquettes incorrect.

## <a name="combine-multiple-results"></a>Combiner plusieurs résultats

Le **évaluer le modèle** module génère une table avec une seule ligne qui contient les différentes mesures. Pour créer un seul ensemble de résultats de précision, nous utilisons tout d’abord **Add Rows** pour combiner les résultats dans une table unique. Nous utilisons ensuite le script Python suivant dans le **exécuter le Script Python** module pour ajouter le nom du modèle et l’approche de formation pour chaque ligne dans la table de résultats :

```
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

Pour afficher les résultats de l’expérience, vous pouvez cliquer sur la sortie de visualiser de la dernière **Select Columns in Dataset** module.

![Visualiser la sortie](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

La première colonne répertorie l’algorithme utilisé pour générer le modèle d’apprentissage.
La deuxième colonne indique le type de l’ensemble d’apprentissage.
La troisième colonne contient la valeur de précision du coût des accents.

À partir de ces résultats, vous pouvez voir que la précision maximale est fournie par le modèle a été créé avec **Two-Class Support Vector Machine** et formé sur le dataset d’apprentissage répliquées.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 : régression : Prédire les prix d’un véhicule automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 2 - régression : Comparer des algorithmes pour la prédiction de prix des véhicules automobiles](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification : Prédire le risque de crédit](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 5 - Classification : Prédire l’évolution](ui-sample-classification-predict-churn.md)
