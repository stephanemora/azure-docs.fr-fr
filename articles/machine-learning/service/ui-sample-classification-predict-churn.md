---
title: 'Classification : Prédire l’évolution, appétence et la vente incitative '
titleSuffix: Azure Machine Learning service
description: Cet exemple d’expérience interface visuelle montre la prédiction de classifieur binaire de l’activité, une tâche courante pour la gestion de la relation client (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 1cb533348236905b7c4e9b58968041745af0e71b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028439"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemple 5 - Classification : Prédire l’évolution, appétence et la vente incitative 

Cet exemple d’expérience interface visuelle montre la prédiction de classifieur binaire d’ATTRITION, appétence et la vente incitative, une tâche courante pour la gestion de la relation client (CRM).

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le **Open** bouton de l’expérience de l’exemple 5.

    ![Ouvrez l’expérience](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Données

Les données que nous utilisons pour cette expérience sont à partir de la KDD Cup 2009. Le jeu de données a 50 000 lignes et colonnes de fonctionnalités 230. La tâche consiste à prédire l’évolution, appétence et la vente incitative pour les clients qui utilisent ces fonctionnalités. Consultez le [site Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009) pour plus d’informations sur les données et la tâche.

## <a name="experiment-summary"></a>Résumé de l’expérience

Voici le graphique d’expérience complète :

![Graphique d’expérience](./media/ui-sample-classification-predict-churn/experiment-graph.png)

Tout d’abord, nous faisons un traitement de données simple.

- Le jeu de données brutes contient un grand nombre de valeurs manquantes. Nous utilisons le **Clean Missing Data** des valeurs de module pour remplacer le manquant avec 0.

    ![Nettoyer le jeu de données](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Les fonctionnalités et le correspondantes d’évolution du, appétence, et la vente incitative les étiquettes se trouvent dans différents jeux de données. Nous utilisons le **Add Columns** module à ajouter les colonnes d’étiquettes pour les colonnes de fonctionnalités. La première colonne, **Col1**, est la colonne d’étiquette. Le reste des colonnes, **Var1**, **Var2**, et ainsi de suite, sont les colonnes de fonctionnalités.
 
    ![Ajouter le jeu de données de colonne](./media/ui-sample-classification-predict-churn/added-column1.png)

- Nous utilisons le **fractionner les données** module pour fractionner le jeu de données en train et ensembles de tests.


    Ensuite, nous utilisons le classifieur binaire d’arbre de décision optimisé avec les paramètres par défaut pour générer des modèles de prévision. Nous créons un modèle par tâche, autrement dit, un seul modèle pour prédire la vente incitative, appétence et l’activité.

## <a name="results"></a>Résultats

Visualiser la sortie de la **évaluer le modèle** module afin de voir les performances du modèle sur le jeu de test. Pour la tâche de la vente incitative, la courbe ROC que le modèle n’apparaît plus performant qu’un modèle aléatoire. La zone sous la courbe (ASC) est 0.857. Au seuil de 0,5, la précision est 0,7, le rappel est 0.463 et le score F1 est 0.545.

![Évaluer les résultats](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Vous pouvez déplacer le **seuil** slider et voir les métriques modifier pour la tâche de classification binaire.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 : régression : Prédire les prix d’un véhicule automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 2 - régression : Comparer des algorithmes pour la prédiction de prix des véhicules automobiles](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification : Prédire le risque de crédit](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 4 : Classification : Prédire le risque de crédit (coût sensible)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
