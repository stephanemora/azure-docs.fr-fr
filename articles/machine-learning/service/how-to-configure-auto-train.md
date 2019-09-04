---
title: Créer des expériences de machine learning automatisé
titleSuffix: Azure Machine Learning service
description: Le machine learning automatisé choisit un algorithme pour vous et génère un modèle prêt pour le déploiement. Découvrez les options que vous pouvez utiliser pour configurer les expériences de machine learning automatisé.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: a73d048a66c70eaf22caf6b33c4a495df6d730dd
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997919"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurer des expériences ML automatisées dans Python

Dans ce guide, découvrez comment définir différents paramètres de configuration de votre expérience d’apprentissage automatique avec le [kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Le machine learning automatisé choisit pour vous un algorithme et des hyperparamètres, et génère un modèle prêt pour le déploiement. Vous disposez de plusieurs options pour configurer les expériences de machine learning automatisé.

Pour voir des exemples d’expérience de machine learning automatisé, consultez [Tutoriel : Entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) ou [Entraîner des modèles avec le machine learning automatisé dans le cloud](how-to-auto-train-remote.md).

Options de configuration disponibles dans le machine learning automatisé :

* Sélectionnez votre type d’expérience : Classification, régression ou prévision de séries chronologiques
* Source de données, formats et récupération de données
* Choisir votre cible de calcul (locale ou distante)
* Paramètres de l’expérience de machine learning automatisé
* Exécuter une expérience de machine learning automatisé
* Explorer les métriques du modèle
* Inscrire et déployer un modèle

Si vous préférez une expérience sans code, vous pouvez également [Créer vos expériences d’apprentissage automatique dans le portail Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Sélectionner le type de votre expérience

Avant de commencer votre expérience, vous devez déterminer le type de problème de machine learning que vous résolvez. Le machine learning automatisé prend en charge les types de tâches de classification, de régression et de prévision.

Le machine learning automatisé prend en charge les algorithmes suivants lors du processus d’automatisation et d’optimisation. En tant qu’utilisateur, vous n’avez pas besoin de spécifier l’algorithme.

classification ; | régression ; | Prévision de séries chronologiques
|-- |-- |--
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
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Utilisez le paramètre `task` dans le constructeur `AutoMLConfig` pour spécifier le type de votre expérience.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Source et format des données
Le machine learning automatisé prend en charge les données qui se trouvent sur votre poste de travail local ou dans le cloud, comme Stockage Blob Azure. Les données peuvent être lues dans les formats de données pris en charge par scikit-learn. Vous pouvez lire les données dans :
* Tableaux Numpy X (caractéristiques) et Y (variable cible, également appelée « étiquette »)
* Tramedonnées Pandas

>[!Important]
> Configuration requise pour les données de formation :
>* Les données doivent être sous forme tabulaire.
>* La valeur que vous souhaitez prédire (colonne cible) doit être présente dans les données.

Exemples :

*   Tableaux Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Tramedonnées Pandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Récupérer des données pour exécuter une expérience sur une capacité de calcul distante

Pour les exécutions à distance, vous devez rendre les données accessibles à partir du calcul distant. Pour ce faire, vous pouvez charger les données vers DataStore.

Voici un exemple d’utilisation de `datastore` :

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Définir des références dprep

Définissez X et y en tant que référence dprep, qui seront passées à l’objet `AutoMLConfig` du machine learning automatisé comme ci-dessous :

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Données pour l’entraînement et la validation

Vous pouvez spécifier des jeux de données distincts pour l’entraînement et la validation directement dans la méthode `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Validation croisée K-Folds

Utilisez le paramètre `n_cross_validations` pour spécifier le nombre de validations croisées. Le jeu de données d’entraînement est divisé en `n_cross_validations` tranches de taille égale. Lors de chaque passe de validation croisée, une des tranches est utilisée pour la validation du modèle entraîné sur les tranches restantes. Ce processus se répète pour `n_cross_validations` passes jusqu’à ce que chaque tranche soit utilisée une fois comme jeu de validation. Les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validation Monte Carlo Cross (sous-échantillonnage aléatoire répété)

Utilisez `validation_size` pour spécifier le pourcentage du jeu de données d’entraînement qui doit être utilisé pour la validation, et utilisez `n_cross_validations` pour spécifier le nombre de validations croisées. Lors de chaque passe de validation croisée, un sous-ensemble de taille `validation_size` est sélectionné de façon aléatoire pour la validation du modèle entraîné sur les données restantes. Enfin, les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement. Monte Carlo n’est pas pris en charge pour la prévision de séries chronologiques.

### <a name="custom-validation-dataset"></a>Jeu de données de validation personnalisé

Utilisez un jeu de données de validation personnalisé si la division aléatoire n’est pas acceptable – en général, des données de séries chronologiques ou des données déséquilibrées. Vous pouvez spécifier votre propre jeu de données de validation. Le modèle est alors évalué par rapport au jeu de données de validation spécifié au lieu du jeu de données aléatoire.

## <a name="compute-to-run-experiment"></a>Capacité de calcul pour exécuter l’expérience

Ensuite, l’endroit où le modèle doit être entraîné est déterminé. Une expérience de machine learning automatisé peut s’exécuter sur les options de calcul suivantes :
*   Votre machine locale, comme un poste de travail local ou un ordinateur portable : en général, quand vous avez un petit jeu de données et que vous êtes toujours dans la phase d’exploration.
*   Une machine distante dans le cloud : la [capacité de calcul managée Azure Machine Learning](concept-compute-target.md#amlcompute) est un service managé qui permet d’entraîner des modèles de machine learning sur des clusters de machines virtuelles Azure.

Consultez le [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) pour obtenir des exemples de notebooks avec des cibles de calcul locales et distantes.

*   Un cluster Azure Databricks dans votre abonnement Azure. Vous trouverez plus de détails ici : [Configurer le cluster Azure Databricks pour ML automatisé](how-to-configure-environment.md#azure-databricks)

Consultez le [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) pour voir des exemples de notebooks avec Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurer les paramètres de votre expérience

Vous disposez de plusieurs options pour configurer votre expérience de machine learning automatisé. Ces paramètres sont définis en instanciant un objet `AutoMLConfig`. Pour obtenir la liste complète des paramètres, reportez-vous à la [Classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

Voici quelques exemples :

1.  Expérience de classification avec AUC pondéré comme métrique principale avec une durée maximale de 12 000 secondes par itération, avec l’expérience se terminant au bout de 50 itérations et 2 tranches de validation croisée.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Voici un exemple d’une expérience de régression définie pour se terminer au bout de 100 itérations, avec chaque itération durant jusqu’à 600 secondes avec 5 tranches de validation croisée.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Les trois valeurs de paramètre `task` différentes déterminent la liste des algorithmes à appliquer.  Utilisez les paramètres `whitelist` ou `blacklist` pour modifier les itérations avec les algorithmes disponibles à inclure ou à exclure. Vous trouverez la liste des modèles pris en charge sur [SupportedAlgorithms classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Métrique principale
La métrique principale ; comme indiqué dans les exemples ci-dessus, elle détermine la métrique à utiliser pendant la formation du modèle dans un but d’optimisation. La métrique principale que vous pouvez sélectionner est déterminée par le type de tâche que vous choisissez. Vous trouverez plus bas une liste des métriques disponibles.

Pour découvrir les définitions spécifiques de celles-ci, voir [Comprendre les résultats du Machine Learning](how-to-understand-automated-ml.md).

|classification ; | régression ; | Prévision de séries chronologiques
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Prétraitement et personnalisation des données

Dans chaque expérience d’apprentissage automatique automatisé, vos données sont [automatiquement mises à l’échelle et normalisées](concept-automated-ml.md#preprocess) pour aider les algorithmes à s’exécuter correctement.  Toutefois, vous pouvez également activer un prétraitement/une personnalisation supplémentaires, tels que l’absence d’imputation des valeurs, de codage et de transformations. [En savoir plus sur la personnalisation incluse](how-to-create-portal-experiments.md#preprocess).

Pour activer cette personnalisation, spécifiez `"preprocess": True` pour la classe [`AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> Les étapes de prétraitement du Machine Learning automatisé (normalisation des fonctionnalités, gestion des données manquantes, conversion de texte en valeurs numériques, etc.) font partie du modèle sous-jacent. Lorsque vous utilisez le modèle pour des prédictions, les étapes de prétraitement qui sont appliquées pendant la formation sont appliquées automatiquement à vos données d’entrée.

### <a name="time-series-forecasting"></a>Prévision de séries chronologiques
Pour le type de tâche prévision de séries chronologiques, vous devez définir des paramètres supplémentaires.
1. time_column_name : paramètre obligatoire qui définit le nom de la colonne dans vos données de formation contenant des séries date/heure.
1. max_horizon : définit la durée pendant laquelle que vous souhaitez prédire sur la base de la périodicité des données de formation. Par exemple si vous avez des données de formation avec des fragments de temps quotidiens, vous définissez jusqu’à quand vous souhaitez que le modèle soit formé.
1. grain_column_names : définit le nom des colonnes qui contiennent des données de séries chronologiques individuelles dans vos données de formation. Par exemple, si vous prévoyez des ventes pour une marque particulière par magasin, vous définirez les colonnes des magasins et des marques comme fragments de colonnes.

Voir ci-dessous des exemples d’utilisation de ces paramètres, un exemple de notebooks est disponible [ici](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a> Configuration des ensembles

Les modèles ensemblistes sont activés par défaut, et leurs itérations apparaissent comme les dernières itérations d’une exécution de Machine Learning automatisé. Les méthodes ensemblistes prises en charge sont le vote et l’empilement. L’implémentation du vote est de type « soft-voting » (vote souple) et utilise des moyennes pondérées. La méthode d’empilement utilise une implémentation à deux couches, dans laquelle la première couche comprend les mêmes modèles que l’ensemble de vote, et où le modèle de la deuxième couche sert à trouver la combinaison optimale des modèles de la première couche. Si vous utilisez des modèles ONNX **ou** si vous avez activé l’explicabilité des modèles, l’empilement est désactivé et seul le vote est utilisé.

Plusieurs arguments par défaut peuvent être fournis en tant que `kwargs` dans un objet `AutoMLConfig` pour modifier le comportement par défaut de l’ensemble d’empilement.

* `stack_meta_learner_type` : ce modèle est entraîné avec la sortie de modèles hétérogènes. Les méta-learners par défaut sont `LogisticRegression` pour les tâches de classification (ou `LogisticRegressionCV`, si la validation croisée est activée) et `ElasticNet` pour les tâches de régression/prévision (ou `ElasticNetCV`, si la validation croisée est activée). Ce paramètre peut correspondre à l’une des chaînes suivantes : `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` ou `LinearRegression`.
* `stack_meta_learner_train_percentage` : spécifie la proportion du jeu d’entraînement (lorsque vous choisissez le type d’entraînement « Entraîner et valider ») à réserver pour l’entraînement du méta-learner. La valeur par défaut est `0.2`.
* `stack_meta_learner_kwargs` : paramètres facultatifs à passer à l’initialiseur du méta-learner. Ces paramètres et types de paramètres reflètent ceux du constructeur de modèle correspondant, et sont transmis à celui-ci.

Le code suivant montre un exemple de spécification d’un comportement d’ensemble personnalisé dans un objet `AutoMLConfig`.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

L’entraînement des ensembles est activé par défaut, mais peut être désactivé à l’aide des paramètres booléens `enable_voting_ensemble` et `enable_stack_ensemble`.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Exécuter une expérience

Pour le Machine Learning automatisé, vous devez créer un objet `Experiment`, qui est un objet nommé d’un `Workspace`, utilisé pour exécuter des expériences.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Soumettez l’expérience pour qu’elle s’exécute et génère un modèle. Passez `AutoMLConfig` à la méthode `submit` pour générer le modèle.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Les dépendances sont d’abord installées sur une nouvelle machine.  Jusqu’à 10 minutes peuvent être nécessaires avant l’affichage de la sortie.
>La définition de `show_output` sur `True` fait que la sortie est affichée sur la console.

### <a name="exit-criteria"></a>Critères de sortie
Vous pouvez définir quelques options pour terminer votre expérience.
1. Aucun critère : si vous ne définissez pas de paramètres de sortie, l’expérience continuera jusqu’à ce que votre métrique principale ne progresse plus.
1. Nombre d’itérations : vous définissez le nombre d’itérations pour l’expérience à exécuter. Facultativement, vous pouvez ajouter iteration_timeout_minutes afin de définir une limite de temps en minutes pour chaque itération.
1. Quitter après un certain temps : avec experiment_timeout_minutes dans vos paramètres, vous pouvez définir la durée en minutes de l’exécution d’une expérience.
1. Quitter après qu’un score a été atteint : avec experiment_exit_score, vous pouvez choisir de terminer l’expérience après qu’un score basé sur votre métrique principale a été atteint.

### <a name="explore-model-metrics"></a>Explorer les métriques du modèle

Vous pouvez consulter vos résultats de formation dans un widget ou en ligne si vous êtes dans un notebook. Pour plus d’informations, consultez [Suivre et évaluer des modèles](how-to-track-experiments.md#view-run-details).

## <a name="understand-automated-ml-models"></a>Comprendre des modèles ML automatisés

Tous les modèles produits à l’aide de ML automatisé comprennent les étapes suivantes :
+ Ingénierie des fonctionnalités automatisées (si prétraiter = True)
+ Mise à l'échelle/Normalisation et algorithme avec valeurs hypermeter

Nous rendons transparente l’obtention de ces informations à partir de la sortie fitted_model de ML automatisé.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Ingénierie des fonctionnalités automatisées

Afficher la liste de prétraitement et [d’ingénierie des fonctionnalités automatisées](concept-automated-ml.md#preprocess) qui se produit lorsque prétraiter = True.

Examinez cet exemple :
+ Il y a 4 fonctionnalités d’entrée : A (numérique), B (numérique), C (numérique), D (DateTime)
+ La fonctionnalité numérique C est supprimée, car il s’agit d’une colonne d’ID avec toutes les valeurs uniques
+ Il manque des valeurs dans les fonctionnalités numériques A et B, par conséquent une moyenne leur est imputée
+ La fonctionnalité DateTime D a 11 fonctionnalités d’ingénierie différentes

Utilisez ces 2 API sur la première étape du modèle ajusté pour en savoir plus.  Consultez le [notebook de cet exemple](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1 : `get_engineered_feature_names()` retourne une liste de noms de fonctionnalités d’ingénierie.

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Cette liste inclut tous les noms des fonctionnalités d’ingénierie.

  >[!Note]
  >Utilisez « timeseriestransformer » pour la tâche=« prévision », utilisez « datatransformer » pour la tâche « régression » ou « classification».

+ API 2 : `get_featurization_summary()` retourne un résumé de personnalisation pour toutes les fonctionnalités d’entrée.

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Utilisez « timeseriestransformer » pour la tâche=« prévision », utilisez « datatransformer » pour la tâche « régression » ou « classification».

  Sortie :
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Où :

   |Output|Définition|
   |----|--------|
   |RawFeatureName|Fonctionnalité/colonne d’entrée du jeu de données fournis.|
   |TypeDetected|Type de données détecté de la fonctionnalité d’entrée.|
   |Supprimé|Indique si la fonctionnalité d’entrée a été supprimée ou utilisée.|
   |EngineeringFeatureCount|Nombre de fonctionnalités générées par le biais de transformations d’ingénierie de la fonctionnalité automatisée.|
   |Transformations|Liste des transformations appliquées à des fonctionnalités d’entrée pour générer des fonctionnalités d’ingénierie.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Mise à l'échelle/Normalisation et algorithme avec valeurs hypermeter :

Pour comprendre les valeurs de mise à l’échelle/normalisation et d’algorithme/hyperparameter pour un pipeline, utilisez fitted_model.steps. [En savoir plus sur la mise à l’échelle/la normalisation](concept-automated-ml.md#preprocess). Voici un exemple de sortie :

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Pour obtenir plus de détails, utilisez cette fonction d’assistance illustrée dans [cet exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Ce qui suit est un exemple de sortie pour un pipeline utilisant un algorithme spécifique (dans ce cas, LogisticRegression avec RobustScalar).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Expliquer le modèle (interprétabilité)

Le machine learning automatisé vous permet de comprendre l’importance des caractéristiques.  Pendant le processus d’entraînement, vous pouvez obtenir l’importance globale des caractéristiques pour le modèle.  Pour les scénarios de classification, vous pouvez également obtenir d’importance des caractéristiques au niveau des classes.  Vous devez fournir un jeu de données de validation (X_valid) pour obtenir l’importance des caractéristiques.

Il y a deux façons de générer l’importance des caractéristiques.

*   Une fois qu’une expérience est terminée, vous pouvez utiliser la méthode `explain_model` sur une des itérations.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Pour visualiser l’importance des caractéristiques pour toutes les itérations, définissez l’indicateur `model_explainability` sur `True` dans AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Quand c’est fait, vous pouvez utiliser la méthode retrieve_model_explanation pour récupérer l’importance des caractéristiques pour une itération spécifique.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

Vous pouvez visualiser le graphique d’importance des caractéristiques dans votre espace de travail du portail Azure. Affichez l’URL à l’aide de l’objet run :

```
automl_run.get_portal_url()
```

Vous pouvez visualiser le graphique d’importance des caractéristiques dans votre espace de travail du portail Azure. Le graphique apparaît également quand vous utilisez le [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails` dans un notebook. Pour en savoir plus sur les graphiques, consultez [Comprendre les résultats du machine learning automatisé](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![Graphique d’importance des caractéristiques](./media/how-to-configure-auto-train/feature-importance.png)

Pour plus d’informations sur la façon dont les explications des modèles et l’importance des fonctionnalités peuvent être activés dans d’autres domaines du kit de développement logiciel (SDK) en dehors de l’apprentissage automatisé, consultez l’article [concept](machine-learning-interpretability-explainability.md) sur l’interprétabilité.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).

Découvrez plus d’informations sur [comment entraîner un modèle de régression avec le machine learning automatisé](tutorial-auto-train-models.md) ou [comment entraîner avec le machine learning automatisé sur une ressource distante](how-to-auto-train-remote.md).
