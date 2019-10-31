---
title: 'Exemple n°5 d’interface visuelle : Classification pour prédire l’évolution, l’appétence et la vente incitative'
titleSuffix: Azure Machine Learning
description: Cet l’échantillon de pipeline d’interface visuelle montre la prédiction de l’attribution, une tâche courante pour la gestion de la relation client (CRM) par le classifieur binaire.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693549"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemple 5 - Classification : Prédire l’évolution, l’appétence et la vente incitative 

Découvrez comment générer un pipeline de Machine Learning complexe à l’aide de l’interface visuelle, sans écrire une seule ligne de code.

Ce pipeline effectue l'apprentissage de classifieurs **Arbre de décision optimisé à deux classes** ou trois pour prédire les tâches courantes pour les systèmes de Gestion de la relation client (CRM) : l’attribution, l’appétence et la vente incitative. Les valeurs de données et les étiquettes sont réparties entre plusieurs sources de données et cryptées pour anonymiser les informations client. Toutefois, nous pouvons toujours utiliser l’interface visuelle pour combiner les jeux de données et effectuer l'apprentissage d’un modèle à l’aide des valeurs cryptées.

Étant donné que vous essayez de répondre à la question « Lequel ? », il s’agit ici d’un problème de classification. Toutefois, vous pouvez appliquer la même logique de ce projet pour résoudre n’importe quel autre type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphique complet associé à ce pipeline :

![Graphique de pipeline](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le bouton **Ouvrir** pour l’échantillon de pipeline 5.

    ![Ouvrir le pipeline](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Données

Les données utilisées pour ce pipeline proviennent du KDD Cup 2009. Elles représentent 50 000 lignes et 230 colonnes de caractéristiques. La tâche consiste à prédire l’évolution, l’appétence et la vente incitative des clients qui utilisent ces fonctionnalités. Pour plus d’informations sur les données et la tâche, consultez le [site web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Résumé du pipeline

Cet échantillon de pipeline d’interface visuelle montre la prédiction de l’attrition, de l’appétence et de la vente incitative, une tâche commune pour la gestion de la relation client (CRM) par le classifieur binaire.

Tout d’abord, quelques exemples de traitement de données simples.

- Le jeu de données brutes a de nombreuses valeurs manquantes. Utilisez le module **Nettoyage des données manquantes** pour remplacer les valeurs manquantes par 0.

    ![Nettoyer le jeu de données](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Les fonctionnalités et les étiquettes d’évolution, d’appétence, et de vente incitative correspondantes se trouvent dans des jeux de données différents. Utilisez le module **Ajouter des colonnes** pour ajouter les colonnes d’étiquettes aux colonnes de caractéristiques. La première colonne, **Col1**, est la colonne d’étiquette. Les autres colonnes (**Var1**, **Var2**, etc.) sont les colonnes de fonctionnalités.

    ![Ajouter le jeu de données de colonne](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Utilisez le module **Fractionner les données** pour diviser le jeu de données d’origine en deux jeux de données distincts pour l’entraînement et le test.

- Utilisez ensuite le classifieur binaire Arbre de décision optimisé avec les paramètres par défaut pour générer les modèles de prédiction. Générez un modèle par tâche, autrement dit, un seul modèle chacun pour prédire la vente incitative, l’appétence et l’évolution.

## <a name="results"></a>Résultats

Visualisez la sortie du module **Évaluer le modèle** afin d’afficher les performances du modèle sur le jeu de test. Pour la tâche de vente incitative, la courbe ROC démontre que le modèle est plus performant qu’un modèle aléatoire. La zone sous la courbe (ASC) est 0.857. Au seuil 0.5, la précision est de 0.7, le rappel est de 0.463 et le score F1 est de 0.545.

![Évaluer les résultats](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Vous pouvez déplacer le curseur **Seuil** et afficher les modifications de métriques pour la tâche de classification binaire.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification : prédire le risque de crédit](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-ui-sample-classification-predict-flight-delay.md)
- [Exemple 7 - Classification de texte : Revues de livres](how-to-ui-sample-text-classification.md)
