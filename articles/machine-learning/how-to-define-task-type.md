---
title: Définir une tâche d’apprentissage automatique pour une exécution automatisée d’apprentissage automatique
titleSuffix: Azure Machine Learning
description: Découvrez comment définir une tâche d’apprentissage automatique pour une exécution automatisée d’apprentissage automatique
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653252"
---
# <a name="how-to-define-a-machine-learning-task"></a>Comment définir une tâche d’apprentissage automatique 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez les tâches d’apprentissage automatique prises en charge et comment les définir pour une exécution test automatisée d’apprentissage automatique (ML automatisé).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Qu’est-ce qu’une tâche d’apprentissage automatique ?

Une tâche d’apprentissage automatique représente le type de problème résolu en créant un modèle prédictif. ML automatisé prend en charge trois différents types de tâches : la classification, la régression et la prévision de séries chronologiques.

Type de tâche| Description| Exemple
----|----|----
classification ; | Tâche de prédiction de la catégorie d’une ligne particulière dans un jeu de données. | Détection des fraudes sur une carte de crédit. La colonne cible serait **Fraude détectée** avec des catégories *Vrai* ou *Faux*. Dans ce cas, nous classons chaque ligne des données comme vraie ou fausse.
régression ; | Tâche de prédiction d’une sortie de quantité continue. | Coût d’automobile basé sur ses fonctionnalités, la colonne cible serait**Prix**.
Prévisions |Tâche permettant d’établir des estimations éclairées afin de déterminer la direction des tendances futures.| Prévision de la demande d’énergie pour les 48 heures suivantes. La colonne cible serait **Demande** et les valeurs prévues seraient utilisées pour montrer les tendances de la demande d’énergie.

Le machine learning automatisé prend en charge les algorithmes suivants lors du processus d’automatisation et d’optimisation. En tant qu’utilisateur, vous n’avez pas besoin de spécifier l’algorithme.

classification ; | régression ; | Prévision de séries chronologiques
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-Support Vector Classification (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classifieur DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Régresseur DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Régresseur DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classifieur linéaire DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Régression linéaire](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Régression linéaire](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Définir le type de tâche
Vous pouvez définir le type de tâche pour vos expériences de ML automatisé à l’aide du Kit de développement logiciel (SDK) ou d’Azure Machine Learning Studio.

Utilisez le paramètre `task` dans le constructeur `AutoMLConfig` pour spécifier le type de votre expérience.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Vous pouvez définir la tâche dans le cadre de la création de vos exécutions tests de ML automatisé dans Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Sélection du type de tâche](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Étapes suivantes

+ Apprenez-en davantage sur le [ML automatisé](concept-automated-ml.md) dans Azure Machine Learning.
+ En savoir plus sur l’[auto-formation d’un modèle de prévision de série chronologique](how-to-auto-train-forecast.md) dans Azure Machine Learning
+ Essayez le tutoriel sur la [classification de Machine Learning automatisé](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).
+ Testez l’exemple de notebook [Régression du Machine Learning automatisé](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb).

