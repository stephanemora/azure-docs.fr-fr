---
title: Créer des expériences de machine learning automatisé
titleSuffix: Azure Machine Learning
description: Le machine learning automatisé choisit un algorithme pour vous et génère un modèle prêt pour le déploiement. Découvrez les options que vous pouvez utiliser pour configurer les expériences de machine learning automatisé.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 3c917912e50c864f49abd5afcd28df4633702f0f
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993710"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurer des expériences ML automatisées dans Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

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

Si vous préférez une expérience sans code, vous pouvez également [créer vos expériences de machine learning automatisé dans Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Sélectionner le type de votre expérience

Avant de commencer votre expérience, vous devez déterminer le type de problème de machine learning que vous résolvez. Le Machine Learning automatisé prend en charge les types de tâches de classification, de régression et de prévision. Découvrez plus d’informations sur les [types de tâches](how-to-define-task-type.md).

Le machine learning automatisé prend en charge les algorithmes suivants lors du processus d’automatisation et d’optimisation. En tant qu’utilisateur, vous n’avez pas besoin de spécifier l’algorithme.

> [!NOTE]
> Si vous envisagez d’exporter vos modèles créés de ML automatisé vers un [modèle ONNX](concept-onnx.md), seuls les algorithmes indiqués par * peuvent être convertis au format ONNX. Apprenez-en davantage sur la [conversion de modèles au format ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Notez également qu’ONNX prend en charge uniquement les tâches de classification et de régression pour l’instant. 

classification ; | régression ; | Prévision de séries chronologiques
|-- |-- |--
[Régression logistique](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Boosting de gradient](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Boosting de gradient](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Arbre de décision](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Arbre de décision](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K plus proches voisins](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K plus proches voisins](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Support Vector Classification (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Forêt aléatoire](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Forêt aléatoire](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Arborescences extrêmement aléatoires](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Arborescences extrêmement aléatoires](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classifieur DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[Régresseur DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Régresseur DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classifieur linéaire DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Régression linéaire](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Régression linéaire](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[FastLinearRegressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[OnlineGradientDescentRegressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[AveragedPerceptronClassifier](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Classifieur SVM linéaire](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Utilisez le paramètre `task` dans le constructeur `AutoMLConfig` pour spécifier le type de votre expérience.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Source et format des données

Le machine learning automatisé prend en charge les données qui se trouvent sur votre poste de travail local ou dans le cloud, comme Stockage Blob Azure. Les données peuvent être lues dans un **DataFrame Pandas** ou un **TabularDataset Azure Machine Learning**.  [En savoir plus sur les jeux de données](how-to-create-register-datasets.md).

Configuration requise pour les données de formation :
- Les données doivent être sous forme tabulaire.
- La valeur à prédire, la colonne cible, doit figurer dans les données.

Les exemples de code suivants montrent comment stocker les données dans ces formats.

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Tramedonnées Pandas

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Récupérer des données pour exécuter une expérience sur une capacité de calcul distante

Pour les exécutions à distance, les données d’entraînement doivent être accessibles à partir de la ressource de calcul distante. La classe [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) dans le SDK expose les fonctionnalités pour :

* transférer facilement des données de fichiers statiques ou d’URL sources vers votre espace de travail
* rendre vos données disponibles pour l’entraînement des scripts dans le cas de l’exécution sur des ressources de calcul cloud

Pour obtenir un exemple d’utilisation de la classe `Dataset` pour monter des données sur la cible de calcul, consultez ce [guide pratique](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets).

## <a name="train-and-validation-data"></a>Données pour l’entraînement et la validation

Vous pouvez spécifier des jeux de données distincts pour l’entraînement et la validation directement dans le constructeur `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Validation croisée K-Folds

Utilisez le paramètre `n_cross_validations` pour spécifier le nombre de validations croisées. Le jeu de données d’entraînement est divisé en `n_cross_validations` tranches de taille égale. Lors de chaque passe de validation croisée, une des tranches est utilisée pour la validation du modèle entraîné sur les tranches restantes. Ce processus se répète pour `n_cross_validations` passes jusqu’à ce que chaque tranche soit utilisée une fois comme jeu de validation. Les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validation Monte-Carlo
 Cross (sous-échantillonnage aléatoire répété)

Utilisez `validation_size` pour spécifier le pourcentage du jeu de données d’entraînement qui doit être utilisé pour la validation, et utilisez `n_cross_validations` pour spécifier le nombre de validations croisées. Lors de chaque passe de validation croisée, un sous-ensemble de taille `validation_size` est sélectionné de façon aléatoire pour la validation du modèle entraîné sur les données restantes. Enfin, les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement. Monte-Carlo
 n’est pas pris en charge pour la prévision de séries chronologiques.

### <a name="custom-validation-dataset"></a>Jeu de données de validation personnalisé

Utilisez un jeu de données de validation personnalisé si la division aléatoire n’est pas acceptable – en général, des données de séries chronologiques ou des données déséquilibrées. Vous pouvez spécifier votre propre jeu de données de validation. Le modèle est alors évalué par rapport au jeu de données de validation spécifié au lieu du jeu de données aléatoire.

## <a name="compute-to-run-experiment"></a>Capacité de calcul pour exécuter l’expérience

Ensuite, l’endroit où le modèle doit être entraîné est déterminé. Une expérience de machine learning automatisé peut s’exécuter sur les options de calcul suivantes :
* Votre machine locale, comme un poste de travail local ou un ordinateur portable : en général, quand vous avez un petit jeu de données et que vous êtes toujours dans la phase d’exploration.
* Une machine distante dans le cloud : la [capacité de calcul managée Azure Machine Learning](concept-compute-target.md#amlcompute) est un service managé qui permet d’entraîner des modèles de machine learning sur des clusters de machines virtuelles Azure.

  Consultez ce [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) pour obtenir des exemples de notebooks avec des cibles de calcul locales et distantes.

* Un cluster Azure Databricks dans votre abonnement Azure. Vous trouverez plus de détails ici : [Configurer le cluster Azure Databricks pour ML automatisé](how-to-configure-environment.md#azure-databricks)

  Consultez ce [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) pour voir des exemples de notebooks avec Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurer les paramètres de votre expérience

Vous disposez de plusieurs options pour configurer votre expérience de machine learning automatisé. Ces paramètres sont définis en instanciant un objet `AutoMLConfig`. Pour obtenir la liste complète des paramètres, reportez-vous à la [Classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

Voici quelques exemples :

1. Expérience de classification utilisant l’AUC pondéré comme métrique principale avec le délai d’expiration (exprimé en minutes) défini sur 30 minutes et 2 plis de validation croisée.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Voici un exemple d’une expérience de régression définie pour se terminer au bout de 60 minutes, avec cinq plis de validation croisée.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Les trois valeurs différentes du paramètre `task` (le troisième type de tâche `forecasting` utilise un pool d’algorithmes similaire à celui des tâches `regression`) déterminent la liste des modèles à appliquer. Utilisez les paramètres `whitelist` ou `blacklist` pour modifier les itérations avec les modèles disponibles à inclure ou à exclure. La liste des modèles pris en charge est accessible dans [Classe SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) pour les tâches [classification](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [prévisions](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting) et [régression](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression).

Pour éviter les échecs de délais d’expérimentation, le service de validation du ML automatisé exige que `experiment_timeout_minutes` soit défini sur un minimum de 15 minutes, soit 60 minutes si la taille de ligne par colonne dépasse 10 millions.

### <a name="primary-metric"></a>Métrique principale
La métrique principale détermine la métrique à utiliser pendant l’entraînement du modèle dans un but d’optimisation. Les métriques disponibles que vous pouvez sélectionner sont déterminées par le type de tâche choisi. Le tableau ci-dessous présente les métriques principales valides pour chaque type de tâche.

|classification ; | régression ; | Prévision de séries chronologiques
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Pour découvrir les définitions spécifiques de ces métriques, consultez [Comprendre les résultats du Machine Learning automatisé](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Caractérisation de données

Dans chaque expérience d’apprentissage automatique automatisée, vos données sont [automatiquement mises à l’échelle et normalisées](concept-automated-ml.md#preprocess) pour faciliter l’exécution de *certains* algorithmes qui sont sensibles aux caractéristiques d’échelles différentes.  Toutefois, vous pouvez également activer des fonctions supplémentaires, telles que l’imputation des valeurs manquantes, l’encodage et les transformations. [En savoir plus sur la personnalisation incluse](how-to-use-automated-ml-for-ml-models.md#featurization).

Lorsque vous configurez vos expériences, vous pouvez activer le paramètre avancé `featurization`. Le tableau suivant présente les paramètres acceptés pour la caractérisation dans la [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Configuration de la caractérisation | Description |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indique que l’étape de caractérisation personnalisée doit être utilisée. [Découvrez comment personnaliser la caractérisation](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| Indique que l’étape de caractérisation ne doit pas être automatique.|
|`"featurization": 'auto'`| Indique que, dans le cadre du prétraitement, des [étapes de garde-fous des données et de caractérisation](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) sont automatiques.|

> [!NOTE]
> Les étapes de caractérisation du Machine Learning automatisé (normalisation des fonctionnalités, gestion des données manquantes, conversion de texte en valeurs numériques, etc.) font partie du modèle sous-jacent. Lorsque vous utilisez le modèle pour des prédictions, les étapes de caractérisation qui sont appliquées pendant la formation sont appliquées automatiquement à vos données d’entrée.

### <a name="time-series-forecasting"></a>Prévision de séries chronologiques
La tâche `forecasting` de prévision de séries chronologiques nécessite des paramètres supplémentaires dans l’objet de configuration :

1. `time_column_name`: paramètre obligatoire qui définit le nom de la colonne dans vos données d’entraînement contenant une série chronologique valide.
1. `max_horizon`: définit la durée pendant laquelle vous souhaitez prédire sur la base de la périodicité des données d’entraînement. Par exemple si vous avez des données de formation avec des fragments de temps quotidiens, vous définissez jusqu’à quand vous souhaitez que le modèle soit formé.
1. `grain_column_names`: Définit le nom des colonnes qui contiennent des données de séries chronologiques individuelles dans vos données d’entraînement. Par exemple, si vous prévoyez des ventes pour une marque particulière par magasin, vous définirez les colonnes des magasins et des marques comme fragments de colonnes. Des séries chronologiques et des prévisions distinctes sont créées pour chaque grain/regroupement. 

Pour obtenir des exemples des paramètres utilisés ci-dessous, consultez l’[exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble-configuration"></a><a name="ensemble"></a> Configuration des ensembles

Les modèles ensemblistes sont activés par défaut, et leurs itérations apparaissent comme les dernières itérations d’une exécution de Machine Learning automatisé. Les méthodes ensemblistes prises en charge sont le vote et l’empilement. L’implémentation du vote est de type « soft-voting » (vote souple) et utilise des moyennes pondérées. La méthode d’empilement utilise une implémentation à deux couches, dans laquelle la première couche comprend les mêmes modèles que l’ensemble de vote, et où le modèle de la deuxième couche sert à trouver la combinaison optimale des modèles de la première couche. Si vous utilisez des modèles ONNX **ou** si vous avez activé l’explicabilité des modèles, l’empilement est désactivé et seul le vote est utilisé.

Plusieurs arguments par défaut peuvent être fournis en tant que `kwargs` dans un objet `AutoMLConfig` pour modifier le comportement par défaut de l’ensemble d’empilement.

* `stack_meta_learner_type` : ce modèle est entraîné avec la sortie de modèles hétérogènes individuels. Les méta-learners par défaut sont `LogisticRegression` pour les tâches de classification (ou `LogisticRegressionCV`, si la validation croisée est activée) et `ElasticNet` pour les tâches de régression/prévision (ou `ElasticNetCV`, si la validation croisée est activée). Ce paramètre peut correspondre à l’une des chaînes suivantes : `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` ou `LinearRegression`.
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
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

L’entraînement des ensembles est activé par défaut, mais peut être désactivé à l’aide des paramètres booléens `enable_voting_ensemble` et `enable_stack_ensemble`.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
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

### <a name="exit-criteria"></a><a name="exit"></a> Critères de sortie

Vous pouvez définir quelques options pour terminer votre expérience.
1. Aucun critère : si vous ne définissez pas de paramètres de sortie, l’expérience continuera jusqu’à ce que votre métrique principale ne progresse plus.
1. Quitter après un certain temps : utilisez `experiment_timeout_minutes` dans vos paramètres pour définir la durée en minutes pendant laquelle l’expérience doit s’exécuter.
1. Quitter une fois qu’un score a été atteint : si vous utilisez `experiment_exit_score`, l’expérience se termine après qu’un score de métrique principal a été atteint.

### <a name="explore-model-metrics"></a>Explorer les métriques du modèle

Vous pouvez consulter vos résultats de formation dans un widget ou en ligne si vous êtes dans un notebook. Pour plus d’informations, consultez [Suivre et évaluer des modèles](how-to-track-experiments.md#view-run-details).

## <a name="understand-automated-ml-models"></a>Comprendre des modèles ML automatisés

Tous les modèles produits à l’aide de ML automatisé comprennent les étapes suivantes :
+ Ingénierie des fonctionnalités automatisées (si `"featurization": 'auto'`)
+ Mise à l’échelle/Normalisation et algorithme avec des valeurs d’hyperparamètre

Nous rendons transparente l’obtention de ces informations à partir de la sortie fitted_model de ML automatisé.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Ingénierie des fonctionnalités automatisées

Afficher la liste de prétraitement et [d’ingénierie des fonctionnalités automatisées](concept-automated-ml.md#preprocess) qui se produit lorsque `"featurization": 'auto'`.

Examinez cet exemple :
+ Il existe quatre fonctionnalités d’entrée : A (numérique), B (numérique), C (numérique), D (DateTime)
+ La fonctionnalité numérique C est supprimée, car il s’agit d’une colonne d’ID avec toutes les valeurs uniques
+ Il manque des valeurs dans les caractéristiques numériques A et B : une moyenne leur est donc affectée
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
  >Utilisez 'timeseriestransformer' pour task='forecasting', sinon utilisez 'datatransformer' pour la tâche 'regression' ou 'classification'.

+ API 2 : `get_featurization_summary()` retourne un résumé de personnalisation pour toutes les fonctionnalités d’entrée.

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Utilisez 'timeseriestransformer' pour task='forecasting', sinon utilisez 'datatransformer' pour la tâche 'regression' ou 'classification'.

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
   
### <a name="customize-feature-engineering"></a>Personnaliser l’ingénierie des caractéristiques
Pour personnaliser l’ingénierie des caractéristiques, spécifiez `"featurization": FeaturizationConfig`.

La personnalisation prise en charge comprend les éléments suivants :

|Personnalisation|Définition|
|--|--|
|Mise à jour de l’objectif de la colonne|Remplacer le type de caractéristique pour la colonne spécifiée.|
|Mise à jour des paramètres du transformateur |Mettre à jour les paramètres du transformateur spécifié. Prend actuellement en charge Imputer (moyen, le plus fréquent et médian) et HashOneHotEncoder.|
|Supprimer des colonnes |Colonnes à supprimer de la caractérisation.|
|Transformateurs de blocs| Transformateurs de blocs à utiliser dans le processus de caractérisation.|

Créez l’objet FeaturizationConfig à l’aide d’appels d’API :
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Mise à l’échelle/Normalisation et algorithme avec des valeurs d’hyperparamètre :

Pour comprendre les valeurs de mise à l’échelle/normalisation et d’algorithme/hyperparameter pour un pipeline, utilisez fitted_model.steps. [En savoir plus sur la mise à l’échelle/la normalisation](concept-automated-ml.md#preprocess). Voici un exemple de sortie :

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Pour obtenir plus de détails, utilisez cette fonction d’assistance : 

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


print_model(model)
```

L’exemple de sortie suivant concerne un pipeline utilisant un algorithme spécifique (dans ce cas, LogisticRegression avec RobustScalar).

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

### <a name="predict-class-probability"></a>Prédire une probabilité de classe

Les modèles produits à l’aide de Machine Learning automatisé ont tous des objets wrapper qui reflètent la fonctionnalité de leur classe d’origine open source. La plupart des objets wrapper de modèle de classification retournés par le Machine Learning automatisé implémentent la fonction `predict_proba()` qui accepte un échantillon de données de matrice avec pointillés ou de type tableau de vos fonctionnalités (valeurs X) et retourne un tableau à n dimensions de chaque échantillon et sa probabilité de classe respective.

En supposant que vous avez récupéré le meilleur modèle exécuté et ajusté en utilisant les appels ci-dessus, vous pouvez appeler `predict_proba()` directement à partir du modèle ajusté, en fournissant un exemple de `X_test` au format approprié en fonction du type de modèle.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Si le modèle sous-jacent ne prend pas en charge la fonction `predict_proba()` ou si le format est incorrect, une exception spécifique de la classe de modèle est levée. Pour obtenir des exemples de la manière dont cette fonction est implémentée pour différents types de modèles, consultez les documents de référence [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) et [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html).

<a name="explain"></a>

## <a name="model-interpretability"></a>Interprétabilité de modèles

L’interprétabilité de modèles vous permet de comprendre pourquoi vos modèles ont effectué des prédictions, ainsi que les valeurs d’importance des caractéristiques sous-jacentes. Le SDK comprend différents packages pour l’activation des caractéristiques d’interprétabilité de modèles, aussi bien au moment de l’entraînement qu’au moment de l’inférence, pour les modèles locaux et déployés.

Pour obtenir des exemples de code sur la façon d’activer les caractéristiques d’interprétabilité spécifiquement au sein d’expériences de machine learning automatisé, consultez le [guide pratique](how-to-machine-learning-interpretability-automl.md).

Pour obtenir des informations générales sur la façon dont les explications de modèle et l’importance des caractéristiques peuvent être activées dans d’autres domaines du SDK en dehors du machine learning automatisé, consultez l’article de présentation du [concept](how-to-machine-learning-interpretability.md) de l’interprétabilité.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).

Découvrez plus d’informations sur [comment entraîner un modèle de régression avec le machine learning automatisé](tutorial-auto-train-models.md) ou [comment entraîner avec le machine learning automatisé sur une ressource distante](how-to-auto-train-remote.md).
