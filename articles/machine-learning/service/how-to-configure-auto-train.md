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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: c0f8a56df5b41236256115ced0d46a87c5ee91a5
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400247"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurer des expériences ML automatisés dans Python

Dans ce guide, découvrez comment définir différents paramètres de configuration de votre expérience avec d’apprentissage automatique le [kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Le machine learning automatisé choisit pour vous un algorithme et des hyperparamètres, et génère un modèle prêt pour le déploiement. Vous disposez de plusieurs options pour configurer les expériences de machine learning automatisé.

Pour voir des exemples d’expérience de machine learning automatisé, consultez [Tutoriel : Entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) ou [Entraîner des modèles avec le machine learning automatisé dans le cloud](how-to-auto-train-remote.md).

Options de configuration disponibles dans le machine learning automatisé :

* Sélectionnez votre type d’expérience : Régression, classification ou prévision de séries chronologiques
* Source de données, formats et récupération de données
* Choisir votre cible de calcul (locale ou distante)
* Paramètres de l’expérience de machine learning automatisé
* Exécuter une expérience de machine learning automatisé
* Explorer les métriques du modèle
* Inscrire et déployer un modèle

Si vous préférez vous passer d’aucun code, vous pouvez également [créer votre expérience dans le portail Azure d’apprentissage automatique](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Sélectionner le type de votre expérience

Avant de commencer votre expérience, vous devez déterminer le type de problème de machine learning que vous résolvez. Le machine learning automatisé prend en charge les types de tâches de classification, de régression et de prévision.

Le machine learning automatisé prend en charge les algorithmes suivants lors du processus d’automatisation et d’optimisation. En tant qu’utilisateur, vous n’avez pas besoin de spécifier l’algorithme. Alors que les algorithmes de réseau de neurones profond sont disponibles pendant la formation, ML automatisé ne génère pas de modèles de réseau de neurones profond.

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
[Classifieur de réseau de neurones profond](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Régresseur de réseau de neurones profond](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Régresseur de réseau de neurones profond](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classifieur linéaire du réseau de neurones profond](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Régresseur linéaire](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Régresseur linéaire](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Utilisez le `task` paramètre dans le `AutoMLConfig` constructeur pour spécifier le type de votre expérience.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Source et format des données
Le machine learning automatisé prend en charge les données qui se trouvent sur votre poste de travail local ou dans le cloud, comme Stockage Blob Azure. Les données peuvent être lues dans les formats de données pris en charge par scikit-learn. Vous pouvez lire les données dans :
* Tableaux Numpy X (caractéristiques) et Y (variable cible, également appelée « étiquette »)
* Tramedonnées Pandas

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

Si vous utilisez une capacité de calcul distante pour exécuter votre expérience, l’extraction de données doit être encapsulée dans un script Python distinct `get_data()`. Ce script est exécuté sur la capacité de calcul distante où l’expérience de machine learning automatisé est exécutée. `get_data` élimine le besoin d’extraire les données sur le réseau pour chaque itération. Sans `get_data`, votre expérience échoue si vous l’exécutez sur une capacité de calcul distante.

Voici un exemple de `get_data` :

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

Dans votre objet `AutoMLConfig`, vous spécifiez le paramètre `data_script` et vous spécifiez le chemin pour le fichier de script `get_data` comme ci-dessous :

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

Le script `get_data` peut retourner :

Clé | type | Mutuellement exclusif avec    | Description
---|---|---|---
X | Tramedonnées Pandas ou tableaux Numpy | data_train, étiquette, colonnes |  Toutes les caractéristiques pour l’entraînement
y | Tramedonnées Pandas ou tableaux Numpy |   label   | Données des étiquettes pour l’entraînement. Pour la classification, il doit s’agir d’un tableau d’entiers.
X_valid | Tramedonnées Pandas ou tableaux Numpy   | data_train, étiquette | _Facultatif_ données constituant l’ensemble de validation de fonctionnalité. Si non spécifié, X est divisé entre l’entraînement et la validation
y_valid |   Tramedonnées Pandas ou tableaux Numpy | data_train, étiquette | _Facultatif_ Les données d’étiquette pour l’entraînement. Si non spécifié, y est divisé entre l’entraînement et la validation
sample_weight | Tramedonnées Pandas ou tableaux Numpy |   data_train, étiquette, colonnes| _Facultatif_ Une valeur de pondération pour chaque échantillon. À utiliser quand vous voulez affecter des pondérations différentes à vos points de données
sample_weight_valid | Tramedonnées Pandas ou tableaux Numpy | data_train, étiquette, colonnes |    _Facultatif_ Une valeur de pondération pour chaque échantillon de validation. Si non spécifié, sample_weight est divisé entre l’entraînement et la validation
data_train |    Tramedonnées Pandas |  X, y, X_valid, y_valid |    Toutes les données (caractéristiques + étiquette) pour l’entraînement
label | string  | X, y, X_valid, y_valid |  La colonne de data_train qui représente l’étiquette
colonnes | Tableau de chaînes  ||  _Facultatif_ Liste verte de colonnes à utiliser pour les caractéristiques
cv_splits_indices   | Tableau d’entiers ||  _Facultatif_ Liste des index pour diviser les données pour la validation croisée

## <a name="train-and-validation-data"></a>Données pour l’entraînement et la validation

Vous pouvez spécifier des jeux de données distincts pour l’entraînement et la validation via get_data() ou directement dans la méthode `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Validation croisée K-Folds

Utilisez le paramètre `n_cross_validations` pour spécifier le nombre de validations croisées. Le jeu de données d’entraînement est divisé en `n_cross_validations` tranches de taille égale. Lors de chaque passe de validation croisée, une des tranches est utilisée pour la validation du modèle entraîné sur les tranches restantes. Ce processus se répète pour `n_cross_validations` passes jusqu’à ce que chaque tranche soit utilisée une fois comme jeu de validation. Les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo Cross Validation (répétée sous-échantillonnage aléatoire)

Utilisez `validation_size` pour spécifier le pourcentage du jeu de données d’entraînement qui doit être utilisé pour la validation, et utilisez `n_cross_validations` pour spécifier le nombre de validations croisées. Lors de chaque passe de validation croisée, un sous-ensemble de taille `validation_size` est sélectionné de façon aléatoire pour la validation du modèle entraîné sur les données restantes. Enfin, les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement. Monte Carlo n’est pas pris en charge pour la prévision de séries chronologiques.

### <a name="custom-validation-dataset"></a>Jeu de données de validation personnalisé

Utiliser le dataset de validation personnalisée si un fractionnement aléatoire n’est pas acceptable, généralement les données de série chronologique ou les données déséquilibrées. Vous pouvez spécifier votre propre jeu de données de validation. Le modèle est alors évalué par rapport au jeu de données de validation spécifié au lieu du jeu de données aléatoire.

## <a name="compute-to-run-experiment"></a>Capacité de calcul pour exécuter l’expérience

Ensuite, l’endroit où le modèle doit être entraîné est déterminé. Une expérience de machine learning automatisé peut s’exécuter sur les options de calcul suivantes :
*   Votre machine locale, comme un poste de travail local ou un ordinateur portable : en général, quand vous avez un petit jeu de données et que vous êtes toujours dans la phase d’exploration.
*   Une machine distante dans le cloud : la [capacité de calcul managée Azure Machine Learning](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) est un service managé qui permet d’entraîner des modèles de machine learning sur des clusters de machines virtuelles Azure.

Consultez le [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) pour obtenir des exemples de notebooks avec des cibles de calcul locales et distantes.

*   Un cluster Azure Databricks dans votre abonnement Azure. Vous trouverez plus de détails ici - [le programme d’installation Azure Databricks de cluster pour ML automatisée](how-to-configure-environment.md#azure-databricks)

Consultez le [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) par exemple les ordinateurs portables avec Azure Databricks.

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

Les trois différents `task` les valeurs de paramètre déterminent la liste des algorithmes à appliquer.  Utilisez les paramètres `whitelist` ou `blacklist` pour modifier les itérations avec les algorithmes disponibles à inclure ou à exclure. Vous trouverez la liste des modèles pris en charge sur [SupportedAlgorithms classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Métrique principale
La mesure principale ; comme indiqué dans les exemples ci-dessus détermine la métrique à utiliser pendant l’apprentissage du modèle d’optimisation. La métrique principale, que vous pouvez sélectionner est déterminée par le type de tâche que vous choisissez. Voici une liste des mesures disponibles.

|classification ; | régression ; | Prévision de séries chronologiques
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Personnalisation de la & de prétraitement des données

Dans chaque automatisé expérience d’apprentissage, vos données sont [automatiquement mis à l’échelle et normalisées](concept-automated-ml.md#preprocess) pour aider les algorithmes à exécuter correctement.  Toutefois, vous pouvez également activer supplémentaires de prétraitement/caractérisation, tels que l’absence d’imputation des valeurs, de codage et de transformations. [En savoir plus sur la caractérisation est incluse](how-to-create-portal-experiments.md#preprocess).

Pour activer cette personnalisation, spécifiez `"preprocess": True` pour le [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Prévision de séries chronologiques
Pour le type de tâche prévision temps série, vous avez des paramètres supplémentaires à définir.
1. time_column_name - il s’agit d’un paramètre obligatoire qui définit le nom de la colonne dans la formation séries de données conteneur date/heure.
1. max_horizon - définit la durée pendant laquelle que vous souhaitez prédire selon la périodicité des données d’apprentissage. Par exemple si vous avez des données d’apprentissage avec des fragments de temps quotidienne, vous définissez l’amplitude arrière dans jours que vous souhaitez former pour le modèle.
1. grain_column_names - définit le nom des colonnes qui contiennent des données de séries de temps individuelles dans vos données d’apprentissage. Par exemple, si vous sont prévoir les ventes d’une marque particulière par magasin, vous définirait store et marque les colonnes en tant que colonnes de votre niveau de granularité.

Consultez l’exemple de ces paramètres utilisés ci-dessous, l’exemple de bloc-notes est disponible [ici](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

## <a name="run-experiment"></a>Exécuter une expérience

Soumettez l’expérience pour qu’elle s’exécute et génère un modèle. Passez `AutoMLConfig` à la méthode `submit` pour générer le modèle.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Les dépendances sont d’abord installées sur une nouvelle machine.  Jusqu’à 10 minutes peuvent être nécessaires avant l’affichage de la sortie.
>La définition de `show_output` sur `True` fait que la sortie est affichée sur la console.

### <a name="exit-criteria"></a>Critères de sortie
Il quelques options que vous pouvez définir pour terminer votre expérience.
1. Aucun critère - si vous ne définissez aucune ne quitte l’expérience continuera jusqu'à ce qu’aucune progression n’est effectuée sur votre mesure principale de paramètres.
1. Nombre d’itérations - vous définissez le nombre d’itérations pour l’expérience à exécuter. Vous pouvez facultatif ajouter iteration_timeout_minutes pour définir une limite de temps en minutes par chaque itération.
1. Quitte après un certain temps - à l’aide d’experiment_timeout_minutes dans vos paramètres, que vous pouvez définir la durée en minutes doit se poursuivre une expérience dans l’exécution.
1. Quitter après qu’un score a été atteint - à l’aide d’experiment_exit_score, que vous pouvez choisir d’effectuer l’expérience après qu’un score basé sur la mesure de votre principale a été atteint.

### <a name="explore-model-metrics"></a>Explorer les métriques du modèle

Si vous êtes dans un bloc-notes, vous pouvez afficher les résultats de votre formation dans un widget ou de la ligne. Pour plus d’informations, consultez [Suivre et évaluer des modèles](how-to-track-experiments.md#view-run-details).

## <a name="understand-automated-ml-models"></a>Comprendre les modèles ML automatisés

N’importe quel modèle généré à l’aide de ML automatisé comprend les étapes suivantes :
+ Automatisée de l’ingénierie (si prétraiter = True)
+ Normalisation/mise à l’échelle et l’algorithme avec valeurs hypermeter

Nous rendre transparente pour obtenir ces informations à partir de la sortie de fitted_model de ML automatisé.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Conception de fonctionnalités automatisées

Afficher la liste de prétraitement et [automatisée ingénierie](concept-automated-ml.md#preprocess) qui se produit lorsque prétraiter = True.

Examinez cet exemple :
+ Il existe des fonctionnalités d’entrée 4 : A (numérique), B (numérique), C (numérique), D (DateTime)
+ Fonctions numériques C sont supprimée, car il s’agit d’une colonne d’ID avec toutes les valeurs uniques
+ Fonctionnalités numériques A et B ont des valeurs manquantes et par conséquent, sont imputées par moyenne
+ Fonctionnalité de date/heure D est caractérisées en 11 différentes fonctionnalités de l’ingénierie

Utilisez ces 2 API sur la première étape du modèle ajusté pour en savoir plus.  Consultez [cet exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1 : `get_engineered_feature_names()` retourne une liste de noms de l’ingénierie des caractéristiques.

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Cette liste inclut tous les noms de conception de fonctionnalité.

  >[!Note]
  >Utilisez 'timeseriestransformer' pour la tâche = « prévision », utilisez « datatransformer » pour la tâche « régression » ou « classification ».

+ API 2 : `get_featurization_summary()` retourne une caractérisation résumé pour toutes les fonctionnalités d’entrée.

  Usage :
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Utilisez 'timeseriestransformer' pour la tâche = « prévision », utilisez « datatransformer » pour la tâche « régression » ou « classification ».

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

   Où :

   |Sortie|Définition|
   |----|--------|
   |RawFeatureName|Nom de fonctionnalité/colonne d’entrée à partir du jeu de données fourni.|
   |TypeDetected|Type de données détectée de la fonctionnalité d’entrée.|
   |Supprimé|Indique si la fonctionnalité d’entrée a été supprimée ou utilisée.|
   |EngineeringFeatureCount|Nombre de fonctionnalités générées via transformations ingénierie de fonctionnalité.|
   |Transformations|Liste des transformations appliquées pour les fonctionnalités pour générer des caractéristiques conçues d’entrée.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Normalisation/mise à l’échelle et l’algorithme avec valeurs hypermeter :

Pour comprendre les valeurs de mise à l’échelle/normalisation et algorithme/hyperparamètres pour un pipeline, utilisez fitted_model.steps. [En savoir plus sur la mise à l’échelle/normalisation](concept-automated-ml.md#preprocess). Voici un exemple de sortie :

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Pour obtenir plus de détails, utilisez cette fonction d’assistance illustrée [cet exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

Voici un exemple de sortie pour un pipeline à l’aide d’un algorithme spécifique (LogisticRegression avec RobustScalar, dans ce cas).

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

## <a name="explain-the-model-interpretability"></a>Expliquer le modèle (interopérabilité)

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

Vous pouvez visualiser le graphique d’importance des caractéristiques dans votre espace de travail du portail Azure. Le graphique apparaît également quand vous utilisez le widget Jupyter dans un notebook. Pour plus d’informations sur les graphiques, consultez l’article [Exemples de notebooks Azure Machine Learning service.](samples-notebooks.md)

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Graphique d’importance des caractéristiques](./media/how-to-configure-auto-train/feature-importance.png)

Pour plus d’informations sur comment les explications de modèle et l’importance de fonctionnalité peuvent être activées dans d’autres zones du SDK en dehors de l’apprentissage automatique, consultez le [concept](machine-learning-interpretability-explainability.md) article sur ce problème.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).

En savoir plus sur [comment former un modèle de régression avec l’apprentissage automatique](tutorial-auto-train-models.md) ou [comment former à l’aide d’automatisé machine learning sur une ressource distante](how-to-auto-train-remote.md).
