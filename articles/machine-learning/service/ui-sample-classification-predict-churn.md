---
title: 'Exemple n°5 d’interface visuelle : Classification pour prédire l’évolution, l’appétence et la vente incitative'
titleSuffix: Azure Machine Learning service
description: Cet exemple d’expérience d’interface visuelle montre la prédiction de l’évolution, une tâche courante pour la gestion de la relation client (CRM) par le classifieur binaire.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: a2eabdd601fcf350d2076d33882e3d90255fee92
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016469"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemple 5 - Classification : Prédire l’évolution, l’appétence et la vente incitative 

Apprenez à créer une expérience d’apprentissage automatique complexe à l’aide de l’interface visuelle, sans écrire une seule ligne de code.

Cette expérience entraîne trois classifieurs **Arbre de décision optimisé à deux classes** pour prédire les tâches courantes pour les systèmes de Gestion de la relation client (CRM) : l’évolution, l’appétence et la vente incitative. Les valeurs de données et les étiquettes sont réparties entre plusieurs sources de données et cryptées pour anonymiser les informations client. Toutefois, nous pouvons toujours utiliser l’interface visuelle pour combiner les jeux de données et entraîner un modèle à l’aide des valeurs cryptées.

Étant donné que nous essayons de répondre à la question « Lequel », il s’agit d’un problème de classification. Toutefois, vous pouvez appliquer les mêmes étapes dans cette expérience pour faire face à n’importe quel type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphique complet associé à cette expérience :

![Graphique de l’expérience](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le bouton **Ouvrir** pour accéder à l’exemple d’expérience 5.

    ![Ouvrir l’expérience](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Données

Les données que nous utilisons pour cette expérience proviennent du KDD Cup 2009. Le jeu de données contient 50 000 lignes et 230 colonnes de fonctionnalités. La tâche consiste à prédire l’évolution, l’appétence et la vente incitative des clients qui utilisent ces fonctionnalités. Pour plus d’informations sur les données et la tâche, consultez le [site web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Résumé de l’expérience

Cet exemple d’expérience d’interface visuelle montre la prédiction de l’évolution, de l’appétence et de la vente incitative, une tâche courante pour la gestion de la relation client (CRM) par le classifieur binaire.

Tout d’abord, nous réalisons un traitement de données simple.

- Le jeu de données brutes contient un grand nombre de valeurs manquantes. Nous utilisons le module **Nettoyage des données manquantes** pour remplacer les valeurs manquantes par 0.

    ![Nettoyer le jeu de données](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Les fonctionnalités et les étiquettes d’évolution, d’appétence, et de vente incitative correspondantes se trouvent dans des jeux de données différents. Nous utilisons le module **Ajouter des colonnes** pour ajouter les colonnes d’étiquettes aux colonnes de fonctionnalités. La première colonne, **Col1**, est la colonne d’étiquette. Les autres colonnes (**Var1**, **Var2**, etc.) sont les colonnes de fonctionnalités.

    ![Ajouter le jeu de données de colonne](./media/ui-sample-classification-predict-churn/added-column1.png)

- Nous utilisons le module **Fractionner les données** pour fractionner le jeu de données entre un jeu d’entraînement et un jeu de test.

    Nous utilisons ensuite le classifieur binaire Arbre de décision optimisé avec les paramètres par défaut pour générer des modèles de prédiction. Nous créons un modèle par tâche, autrement dit, un seul modèle chacun pour prédire la vente incitative, l’appétence et l’évolution.

## <a name="results"></a>Résultats

Visualisez la sortie du module **Évaluer le modèle** afin d’afficher les performances du modèle sur le jeu de test. Pour la tâche de vente incitative, la courbe ROC démontre que le modèle est plus performant qu’un modèle aléatoire. La zone sous la courbe (ASC) est 0.857. Au seuil 0.5, la précision est de 0.7, le rappel est de 0.463 et le score F1 est de 0.545.

![Évaluer les résultats](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Vous pouvez déplacer le curseur **Seuil** et afficher les modifications de métriques pour la tâche de classification binaire.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification : prédire le risque de crédit](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 6 - Classification : Prédire les retards de vols](ui-sample-classification-predict-flight-delay.md)
