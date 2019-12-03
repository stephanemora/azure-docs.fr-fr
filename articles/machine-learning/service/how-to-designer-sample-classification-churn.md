---
title: 'Concepteur : Exemple Prédire l’attrition'
titleSuffix: Azure Machine Learning
description: Suivez cet exemple de classification pour prédire l’attrition avec le concepteur Azure Machine Learning et les arbres de décision boostés.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.openlocfilehash: 1fe3598fd15424ab2593e3d236146c7566493743
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225097"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Utiliser l’arbre de décision boosté pour prédire l’attrition avec le concepteur Azure Machine Learning

**Exemple 5 du concepteur (préversion)**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Découvrez comment générer un pipeline de machine learning complexe à l’aide du concepteur (préversion), sans écrire une seule ligne de code.

Ce pipeline entraîne 2 classifieurs **Arbre de décision optimisé à deux classes** pour prédire les tâches courantes pour les systèmes de gestion de la relation client (CRM) : l’attrition clients. Les valeurs de données et les étiquettes sont réparties entre plusieurs sources de données et cryptées pour rendre anonymes les informations client. Toutefois, nous pouvons toujours utiliser le concepteur pour combiner les jeux de données et entraîner un modèle à l’aide des valeurs masquées.

Étant donné que vous essayez de répondre à la question « Lequel ? », il s’agit ici d’un problème de classification. Toutefois, vous pouvez appliquer la même logique de ce projet pour résoudre n’importe quel autre type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphique complet associé à ce pipeline :

![Graphique de pipeline](./media/how-to-designer-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Cliquez sur l’exemple 5 pour l’ouvrir. 

## <a name="data"></a>Données

Les données utilisées pour ce pipeline proviennent du KDD Cup 2009. Elles représentent 50 000 lignes et 230 colonnes de caractéristiques. La tâche consiste à prédire l’évolution, l’appétence et la vente incitative des clients qui utilisent ces fonctionnalités. Pour plus d’informations sur les données et la tâche, consultez le [site web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Résumé du pipeline

Cet exemple de pipeline dans le concepteur montre la prédiction de l’attrition, de l’appétence et de la vente incitative, une tâche commune pour la gestion de la relation client (CRM), par le classifieur binaire.

Tout d’abord, quelques exemples de traitement de données simples.

- Le jeu de données brutes a de nombreuses valeurs manquantes. Utilisez le module **Nettoyage des données manquantes** pour remplacer les valeurs manquantes par 0.

    ![Nettoyer le jeu de données](./media/how-to-designer-sample-classification-predict-churn/cleaned-dataset.png)

- Les caractéristiques et l’attrition correspondante se trouvent dans des jeux de données différents. Utilisez le module **Ajouter des colonnes** pour ajouter les colonnes d’étiquettes aux colonnes de caractéristiques. La première colonne, **Col1**, est la colonne d’étiquette. Dans le résultat de visualisation, nous pouvons voir que le jeu de données est déséquilibré. Il existe plus d’exemples négatifs (-1) que d’exemples positifs (+1). Nous utiliserons ultérieurement le module **SMOTE** pour augmenter les cas sous-représentés.

    ![Ajouter le jeu de données de colonne](./media/how-to-designer-sample-classification-predict-churn/added-column1.png)



- Utilisez le module **Fractionner les données** pour diviser le jeu de données d’origine en deux jeux de données distincts pour l’entraînement et le test.

- Utilisez ensuite le classifieur binaire Arbre de décision optimisé avec les paramètres par défaut pour générer les modèles de prédiction. Générez un modèle par tâche, autrement dit, un seul modèle chacun pour prédire la vente incitative, l’appétence et l’évolution.

- Dans la partie droite du pipeline, nous utilisons le module **SMOTE** pour augmenter le pourcentage d’exemples positifs. Le pourcentage SMOTE est défini sur 100 pour doubler le nombre d’exemples positifs. Découvrez plus d’informations sur la façon dont le module SMOTE fonctionne avec la [reference0 du module SMOTE](../././algorithm-module-reference/SMOTE.md).

## <a name="results"></a>Résultats

Visualisez la sortie du module **Évaluer le modèle** afin d’afficher les performances du modèle sur le jeu de test. 

![Évaluer les résultats](./media/how-to-designer-sample-classification-predict-churn/evaluate-result.png)

 Vous pouvez déplacer le curseur **Seuil** et afficher les modifications de métriques pour la tâche de classification binaire. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres échantillons disponibles pour le concepteur :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification avec sélection des caractéristiques : Prédiction des revenus](how-to-designer-sample-classification-predict-income.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-designer-sample-classification-flight-delay.md)
- [Exemple 7 - Classification de texte : Jeu de données Wikipédia SP 500](how-to-designer-sample-text-classification.md)
