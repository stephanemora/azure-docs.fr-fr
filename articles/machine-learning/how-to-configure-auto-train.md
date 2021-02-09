---
title: Créer des expériences de machine learning automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment définir des sources de données, des calculs et des paramètres de configuration pour vos expériences de Machine Learning automatisé.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 6971d67204beb39ff0afa6c68dbecf278d86b299
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954713"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurer des expériences ML automatisées dans Python


Dans ce guide, découvrez comment définir différents paramètres de configuration de votre expérience d’apprentissage automatique avec le [kit de développement logiciel (SDK) Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Le machine learning automatisé choisit pour vous un algorithme et des hyperparamètres, et génère un modèle prêt pour le déploiement. Vous disposez de plusieurs options pour configurer les expériences de machine learning automatisé.

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

## <a name="prerequisites"></a>Prérequis

Pour cet article, vous avez besoin des éléments suivants : 
* Un espace de travail Azure Machine Learning. Pour créer l’espace de travail, voir [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le kit SDK Python d’Azure Machine Learning installé.
    Pour installer le kit de développement logiciel (SDK), vous pouvez : 
    * Créer une instance de calcul, qui installe automatiquement le kit de développement logiciel (SDK) et est préconfigurée pour les flux de travail ML. Consultez [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md) pour plus d’informations. 

    * [Installez vous-même le package `automl`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), qui comprend l’[installation par défaut](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install) du Kit de développement logiciel (SDK).

## <a name="select-your-experiment-type"></a>Sélectionner le type de votre expérience

Avant de commencer votre expérience, vous devez déterminer le type de problème de machine learning que vous résolvez. La Machine Learning automatisé prend en charge les types de tâches `classification`, `regression` et `forecasting`. Découvrez plus d’informations sur les [types de tâches](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Le code suivant utilise le paramètre `task` dans le constructeur `AutoMLConfig` pour spécifier le type d’expérience comme `classification`.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Source et format des données

Le machine learning automatisé prend en charge les données qui se trouvent sur votre poste de travail local ou dans le cloud, comme Stockage Blob Azure. Les données peuvent être lues dans un **DataFrame Pandas** ou un **TabularDataset Azure Machine Learning**. [En savoir plus sur les jeux de données](how-to-create-register-datasets.md).

Configuration requise pour les données d’apprentissage en lien avec le Machine Learning :
- Les données doivent être sous forme tabulaire.
- La valeur à prédire, la colonne cible, doit figurer dans les données.

**Pour les expériences à distance**, les données d’entraînement doivent être accessibles à partir de la ressource de calcul distante. AutoML accepte uniquement [TabularDatasets Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) lors de l’utilisation d’un calcul distant. 

Les jeux de données Azure Machine Learning exposent les fonctionnalités suivantes :

* Transférez facilement des données de fichiers statiques ou d’URL sources vers votre espace de travail.
* Rendez vos données disponibles pour l’entraînement des scripts dans le cas de l’exécution sur des ressources de calcul cloud. Pour obtenir un exemple d’utilisation de la classe `Dataset` pour monter des données dans votre cible de calcul à distance, consultez [Comment former un modèle avec des jeux de données](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets).

Le code suivant crée un TabularDataset à partir d’une URL web. Consultez [Créer un TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) pour obtenir des exemples de code sur la création de jeux de données à partir d’autres sources, comme des fichiers locaux et des magasins de données.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Pour les expériences de calcul locales**, nous recommandons les dataframes pandas pour accélérer les temps de traitement.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Formation, validation et test des données

Vous pouvez spécifier des **jeux de données d’apprentissage et de validation** distincts directement dans le constructeur `AutoMLConfig`. En savoir plus sur le [guide pratique pour configurer les fractionnements de données et la validation croisée](how-to-configure-cross-validation-data-splits.md) pour vos expériences AutoML. 

Si vous ne spécifiez pas explicitement un paramètre `validation_data` ou `n_cross_validation`, le Machine Learning automatisé applique les techniques par défaut pour déterminer la façon dont la validation est effectuée. Cette détermination dépend du nombre de lignes dans le jeu de données affecté à votre paramètre `training_data`. 

|Formation sur la taille des&nbsp;données&nbsp;| Technique de validation |
|---|-----|
|**Contient plus&nbsp;de&nbsp;20 000&nbsp;lignes**| Le fractionnement des données de formation/validation est appliqué. La valeur par défaut consiste à prendre 10 % du jeu de données d’apprentissage initial en tant que jeu de validation. Ce jeu de validation est ensuite utilisé pour le calcul des métriques.
|**Contient moins&nbsp;de&nbsp;20 000&nbsp;lignes**| L’approche de validation croisée est appliquée. Le nombre de plis par défaut dépend du nombre de lignes. <br> **Si le jeu de données est inférieur à 1 000 lignes**, 10 plis sont utilisés. <br> **S’il y a entre 1 000 et 20 000 lignes**, trois plis sont utilisés.

À ce stade, vous devez fournir vos propres **données de test** pour l’évaluation du modèle. Pour obtenir un exemple de code d’intégration de vos propres données de test pour l’évaluation du modèle, consultez la section **Test** de [ce notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Capacité de calcul pour exécuter l’expérience

Ensuite, l’endroit où le modèle doit être entraîné est déterminé. Une expérience de machine learning automatisé peut s’exécuter sur les options de calcul suivantes. Découvrez [les avantages et les inconvénients des options de calcul locales et distantes](concept-automated-ml.md#local-remote). 

* Votre machine **locale**, comme un poste de travail local ou un ordinateur portable : en général, quand vous avez un petit jeu de données et que vous êtes toujours dans la phase d’exploration. Consultez [ce notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) pour un exemple de calcul local. 
 
* Une machine **distante** dans le cloud : la [capacité de calcul managée Azure Machine Learning](concept-compute-target.md#amlcompute) est un service managé qui permet d’entraîner des modèles de machine learning sur des clusters de machines virtuelles Azure. 

    Consultez [ce notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) pour obtenir un exemple distant utilisant le calcul managé Azure Machine Learning. 

* Un **cluster Azure Databricks** dans votre abonnement Azure. Pour plus de détails, consultez [Configurer un cluster Azure Databricks pour ML automatisé](how-to-configure-databricks-automl-environment.md). Consultez ce [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) pour voir des exemples de notebooks avec Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurer les paramètres de votre expérience

Vous disposez de plusieurs options pour configurer votre expérience de machine learning automatisé. Ces paramètres sont définis en instanciant un objet `AutoMLConfig`. Pour obtenir la liste complète des paramètres, reportez-vous à la [Classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

Voici quelques exemples :

1. Expérience de classification utilisant l’AUC pondéré comme métrique principale avec le délai d’expiration (exprimé en minutes) défini sur 30 minutes et 2 plis de validation croisée.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Voici un exemple d’une expérience de régression définie pour se terminer au bout de 60 minutes, avec cinq plis de validation croisée.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Les tâches de prévision requièrent une configuration supplémentaire. Pour plus d’informations, consultez l’article [Entraîner automatiquement un modèle de prévision de série chronologique](how-to-auto-train-forecast.md). 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
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
    
### <a name="supported-models"></a>Modèles pris en charge

Le machine learning automatisé essaie différents modèles et algorithmes lors du processus d’automatisation et d’optimisation. En tant qu’utilisateur, vous n’avez pas besoin de spécifier l’algorithme. 

Les trois valeurs de paramètre `task` différentes déterminent la liste des algorithmes ou modèles à appliquer. Utilisez les paramètres `allowed_models` ou `blocked_models` pour modifier les itérations avec les modèles disponibles à inclure ou à exclure. 

Le tableau suivant récapitule les modèles pris en charge par type de tâche. 

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
[AveragedPerceptronClassifier](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[OnlineGradientDescentRegressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[FastLinearRegressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Classifieur SVM linéaire](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Métrique principale
Le paramètre `primary metric` détermine la métrique à utiliser pendant l’entraînement du modèle dans un but d’optimisation. Les métriques disponibles que vous pouvez sélectionner sont déterminées par le type de tâche choisi. Le tableau ci-dessous présente les métriques principales valides pour chaque type de tâche.

Le choix d’une métrique principale pour l’optimisation du Machine Learning automatisé dépend de nombreux facteurs. Nous vous recommandons avant tout de choisir une métrique correspondant au mieux à vos besoins métier. Examinez ensuite si la métrique convient pour votre profil de jeu de données (taille des données, plage, distribution de classe, etc.).

Pour découvrir les définitions spécifiques de ces métriques, consultez [Comprendre les résultats du Machine Learning automatisé](how-to-understand-automated-ml.md).

|classification ; | régression ; | Prévision de séries chronologiques
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Principales métriques pour les scénarios de classification 

Il se peut que la publication de métriques seuils telles que `accuracy`, `average_precision_score_weighted`, `norm_macro_recall` et `precision_score_weighted` ne permette pas d’obtenir une bonne optimisation pour des jeux de données de très petite taille ou présentant une asymétrie (déséquilibre) de classe conséquente, ou lorsque la valeur de métrique attendue est très proche de 0,0 ou 1,0. Dans ces cas, `AUC_weighted` peut être un meilleur choix pour la métrique principale. Une fois l’opération de Machine Learning automatisé terminée, vous pouvez choisir le modèle gagnant en fonction de la métrique la plus adaptée à vos besoins métier.

| Métrique | Exemple(s) de cas d’usage |
| ------ | ------- |
| `accuracy` | Classification d’images, analyse des sentiments, prédiction d’attrition |
| `AUC_weighted` | Détection des fraudes, classification d’images, détection d’anomalies, détection de courrier indésirable |
| `average_precision_score_weighted` | analyse de sentiments |
| `norm_macro_recall` | Prédiction d’attrition |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Métriques principales pour les scénarios de régression

Il se peut que des métriques telles que `r2_score` et `spearman_correlation` représentent mieux la qualité du modèle lorsque l’échelle de valeur à prédire couvre de nombreux ordres de grandeur. Par exemple, une estimation de salaire, où de nombreuses personnes perçoivent un salaire compris entre 20 000 et 100 000 dollars, mais où l’échelle va très haut avec certains salaires s’inscrivant dans la plage des 100 millions de dollars. 

Dans ce cas, `normalized_mean_absolute_error` et `normalized_root_mean_squared_error` traitent une erreur de prédiction de 20 000 dollars de la même façon pour un salarié percevant un salaire de 30 000 dollars que pour un salarié percevant 20 millions de dollars. Or, en réalité, prévoir seulement 20 000 dollars de réduction sur un salaire de 20 millions de dollars est insignifiant (petite différence relative de 0,1 %), tandis que 20 000 dollars de réduction sur un salaire de 30 000 dollars est tout à fait conséquent (grande différence relative de 67 %). `normalized_mean_absolute_error` et `normalized_root_mean_squared_error` sont utiles quand les valeurs à prédire sont d’échelle similaire.

| Métrique | Exemple(s) de cas d’usage |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prédiction de prix (maison/produit/pourboire), examen de prédiction de score |
| `r2_score` | Retard de compagnie aérienne, estimation du salaire, temps de résolution des bogues |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Principales métriques pour les scénarios de prévision de séries chronologiques

Consultez les remarques sur les régressions ci-dessus.

| Métrique | Exemple(s) de cas d’usage |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prédiction (prévision) de prix, optimisation de stocks, prévision de la demande |
| `r2_score` | Prédiction (prévision) de prix, optimisation de stocks, prévision de la demande |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Caractérisation de données

Dans chaque expérience d’apprentissage automatique automatisée, vos données sont automatiquement mises à l’échelle et normalisées pour faciliter l’exécution de *certains* algorithmes qui sont sensibles aux caractéristiques d’échelles différentes. Cette mise à l’échelle et la normalisation sont appelées caractérisation. Pour plus d’informations et des exemples de code, consultez [Caractérisation dans AutoML](how-to-configure-auto-features.md#). 

Lorsque vous configurez vos expériences dans votre objet `AutoMLConfig`, vous pouvez activer/désactiver le paramètre `featurization`. Le tableau suivant présente les paramètres acceptés pour la caractérisation dans l’[objet AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configuration de la caractérisation | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indique que, dans le cadre du prétraitement, des [étapes de garde-fous des données et de caractérisation](how-to-configure-auto-features.md#featurization) sont automatiques. **Paramètre par défaut**.|
|`"featurization": 'off'`| Indique que l’étape de caractérisation ne doit pas être automatique.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indique que l’étape de caractérisation personnalisée doit être utilisée. [Découvrez comment personnaliser la caractérisation](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Les étapes de caractérisation du Machine Learning automatisé (normalisation des fonctionnalités, gestion des données manquantes, conversion de texte en valeurs numériques, etc.) font partie du modèle sous-jacent. Lorsque vous utilisez le modèle pour des prédictions, les étapes de caractérisation qui sont appliquées pendant la formation sont appliquées automatiquement à vos données d’entrée.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Configuration des ensembles

Les modèles ensemblistes sont activés par défaut, et leurs itérations apparaissent comme les dernières itérations d’une exécution d’AutoML. Actuellement, **VotingEnsemble** et **StackEnsemble** sont pris en charge. 

Le vote implémente un vote réversible, qui utilise des moyennes pondérées. La méthode d’empilement utilise une implémentation à deux couches, dans laquelle la première couche comprend les mêmes modèles que l’ensemble de vote, et où le modèle de la deuxième couche sert à trouver la combinaison optimale des modèles de la première couche. 

Si vous utilisez des modèles ONNX **ou** si vous avez activé l’explicabilité des modèles, l’empilement est désactivé et seul le vote est utilisé.

La formation des ensembles peut être désactivée à l’aide des paramètres booléens `enable_voting_ensemble` et `enable_stack_ensemble`.

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

Plusieurs arguments par défaut peuvent être fournis en tant que `kwargs` dans un objet `AutoMLConfig` pour modifier le comportement par défaut de l’ensemble.

> [!IMPORTANT]
>  Les paramètres suivants ne sont pas des paramètres explicites de la classe AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Pendant la génération des modèles **VotingEnsemble** et **StackEnsemble**, plusieurs modèles ajustés des exécutions enfants précédentes sont téléchargés. Si vous rencontrez cette erreur : `AutoMLEnsembleException: Could not find any models for running ensembling`, vous devrez peut-être prévoir plus de temps pour le téléchargement des modèles. La valeur par défaut est de 300 secondes pour le téléchargement de ces modèles en parallèle et il n’y a pas de limite maximale pour le délai d’expiration. Si plus de temps est nécessaire, configurez ce paramètre avec une valeur supérieure à 300 secondes. 

  > [!NOTE]
  >  Si le délai d’expiration est atteint et que des modèles sont téléchargés, l’ensemble se poursuit avec autant de modèles qu’il a téléchargés. Il n’est pas nécessaire que tous les modèles soient téléchargés pour terminer dans ce délai.

Les paramètres suivants s’appliquent uniquement aux modèles **StackEnsemble** : 

* `stack_meta_learner_type` : ce modèle est entraîné avec la sortie de modèles hétérogènes individuels. Les méta-learners par défaut sont `LogisticRegression` pour les tâches de classification (ou `LogisticRegressionCV`, si la validation croisée est activée) et `ElasticNet` pour les tâches de régression/prévision (ou `ElasticNetCV`, si la validation croisée est activée). Ce paramètre peut correspondre à l’une des chaînes suivantes : `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` ou `LinearRegression`.

* `stack_meta_learner_train_percentage` : spécifie la proportion du jeu d’entraînement (lorsque vous choisissez le type d’entraînement « Entraîner et valider ») à réserver pour l’entraînement du méta-learner. La valeur par défaut est `0.2`. 

* `stack_meta_learner_kwargs` : paramètres facultatifs à passer à l’initialiseur du méta-learner. Ces paramètres et types de paramètres reflètent ceux du constructeur de modèle correspondant, et sont transmis à celui-ci.

Le code suivant montre un exemple de spécification d’un comportement d’ensemble personnalisé dans un objet `AutoMLConfig`.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

<a name="exit"></a> 

### <a name="exit-criteria"></a>Critères de sortie

Vous pouvez définir quelques options dans votre AutoMLConfig pour terminer votre expérience.

|Critères| description
|----|----
Aucun&nbsp;critère | si vous ne définissez pas de paramètres de sortie, l’expérience continuera jusqu’à ce que votre métrique principale ne progresse plus.
Après&nbsp;une&nbsp;quantité&nbsp;de&nbsp;temps| Utilisez `experiment_timeout_minutes` dans vos paramètres pour définir la durée, en minutes, pendant laquelle votre expérience doit continuer à s’exécuter. <br><br> Pour éviter les échecs d’expiration du délai d’expérience, il y a un minimum de 15 minutes, ou de 60 minutes si la taille de ligne par colonne dépasse 10 millions.
Un&nbsp;score&nbsp;a&nbsp;été&nbsp;atteint| Si vous utilisez `experiment_exit_score`, l’expérience se termine après qu’un score de métrique principal spécifié a été atteint.

## <a name="run-experiment"></a>Exécuter une expérience

Pour le Machine Learning automatisé, vous devez créer un objet `Experiment`, qui est un objet nommé d’un `Workspace`, utilisé pour exécuter des expériences.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
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

### <a name="multiple-child-runs-on-clusters"></a>Plusieurs exécutions enfants sur des clusters

Les exécutions enfants d’expérimentation ML automatisé peuvent intervenir sur un cluster exécutant déjà une autre expérience. Toutefois, le minutage dépend du nombre de nœuds du cluster et de la disponibilité de ces nœuds pour exécuter une expérience différente.

Chaque nœud du cluster fait office de machine virtuelle individuelle capable d’effectuer une seule exécution d’apprentissage ; en termes de ML automatisé, cela signifie une exécution enfant. Si tous les nœuds sont occupés, la nouvelle expérience est mise en file d’attente. Cela étant, si des nœuds sont disponibles, la nouvelle expérience procède à des exécutions enfants de ML automatisé en parallèle dans les nœuds/machines virtuelles disponibles.

Pour faciliter la gestion des exécutions enfants et le moment où elles peuvent intervenir, nous vous recommandons de créer un cluster dédié par expérience et de faire correspondre le nombre de `max_concurrent_iterations` de votre expérience avec le nombre de nœuds du cluster. Ainsi, vous utilisez tous les nœuds du cluster en même temps que les exécutions/itérations enfants simultanées de votre choix.

Configurez `max_concurrent_iterations` dans votre objet `AutoMLConfig`. S’il n’est pas configuré, par défaut, une seule exécution/itération enfant simultanée est autorisée par expérience.  

## <a name="explore-models-and-metrics"></a>Explorer les modèles et les métriques

Vous pouvez consulter vos résultats de formation dans un widget ou en ligne si vous êtes dans un notebook. Pour plus d’informations, consultez [Suivre et évaluer des modèles](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs).

Consultez [Évaluer les résultats d’expérience du machine learning automatisé](how-to-understand-automated-ml.md) pour obtenir des définitions et des exemples des graphiques de performances et des métriques fournis pour chaque exécution. 

Pour obtenir un résumé de caractérisation et comprendre les fonctionnalités qui ont été ajoutées à un modèle particulier, consultez [Transparence de la caractérisation](how-to-configure-auto-features.md#featurization-transparency). 

> [!NOTE]
> Les algorithmes utilisés par le ML automatisé présentent un fonctionnement aléatoire inhérent qui peut provoquer de légères variations dans un score de métrique final de modèle recommandé, comme la précision. Le ML automatisé exécute également des opérations au niveau des données, telles que le fractionnement de test de formation, le fractionnement de validation de formation ou la validation croisée, le cas échéant. Par conséquent, si vous exécutez plusieurs fois une expérience avec les mêmes paramètres de configuration et la même métrique principale, vous constaterez probablement des variations dans chaque score de métrique finale d’expériences en raison de ces facteurs. 

## <a name="register-and-deploy-models"></a>Inscrire et déployer des modèles

Pour plus d’informations sur le téléchargement ou l’inscription d’un modèle de déploiement sur un service Web, consultez [Comment et où déployer un modèle](how-to-deploy-and-where.md).

<a name="explain"></a>

## <a name="model-interpretability"></a>Interprétabilité de modèles

L’interprétabilité de modèles vous permet de comprendre pourquoi vos modèles ont effectué des prédictions, ainsi que les valeurs d’importance des caractéristiques sous-jacentes. Le SDK comprend différents packages pour l’activation des caractéristiques d’interprétabilité de modèles, aussi bien au moment de l’entraînement qu’au moment de l’inférence, pour les modèles locaux et déployés.

Pour obtenir des exemples de code sur la façon d’activer les caractéristiques d’interprétabilité spécifiquement au sein d’expériences de machine learning automatisé, consultez le [guide pratique](how-to-machine-learning-interpretability-automl.md).

Pour obtenir des informations générales sur la façon dont les explications de modèle et l’importance des caractéristiques peuvent être activées dans d’autres domaines du SDK en dehors du machine learning automatisé, consultez l’article de présentation du [concept](how-to-machine-learning-interpretability.md) de l’interprétabilité.

> [!NOTE]
> Le modèle ForecastTCN n’est actuellement pas pris en charge par le client d’explication. Ce modèle ne retourne pas de tableau de bord s’il est retourné comme meilleur modèle et ne prend pas en charge les exécutions d’explications à la demande.

## <a name="troubleshooting"></a>Résolution des problèmes

* **La mise à niveau récente des dépendances `AutoML` vers les versions plus récentes va entraîner une rupture de compatibilité** :  à partir de la version 1.13.0 du kit de développement logiciel (SDK), les modèles ne seront pas chargés dans les kit de développement logiciel (SDK) plus anciens en raison d’une incompatibilité entre les anciennes versions que nous avons épinglées dans nos packages précédents et les nouvelles versions que nous épinglons maintenant. Vous voyez une erreur telle que :
  * Module introuvable : Ex.`No module named 'sklearn.decomposition._truncated_svd`,
  * Erreurs d’importation : Ex.`ImportError: cannot import name 'RollingOriginValidator'`,
  * Erreurs d’attribut : Ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Pour contourner ce problème, effectuez l’une des deux étapes suivantes en fonction de votre version de formation du kit de développement logiciel (SDK) `AutoML` :
    * Si votre version de formation du kit de développement logiciel (SDK) `AutoML` est supérieure à 1.13.0, vous avez besoin de `pandas == 0.25.1` et de `sckit-learn==0.22.1`. En cas d’incompatibilité de version, mettez à niveau scikit-Learn et/ou pandas pour corriger la version, comme indiqué ci-dessous :
      
      ```bash
         pip install --upgrade pandas==0.25.1
         pip install --upgrade scikit-learn==0.22.1
      ```
      
    * Si votre version de formation du kit de développement logiciel (SDK) `AutoML` est inférieure ou égale à 1.12.0, vous avez besoin de `pandas == 0.23.4` et de `sckit-learn==0.20.3`. En cas d’incompatibilité de version, passez à une version antérieure de scikit-Learn et/ou de pandas pour corriger la version, comme indiqué ci-dessous :
  
      ```bash
        pip install --upgrade pandas==0.23.4
        pip install --upgrade scikit-learn==0.20.3
      ```

* **Déploiement ayant échoué** : Pour les versions <= 1.18.0 du kit SDK, l’image de base créée pour le déploiement peut échouer avec l’erreur suivante : « Erreur d’importation : impossible d’importer le nom `cached_property` à partir de `werkzeug` ». 

  La procédure suivante peut permettre de contourner le problème :
  1. Téléchargez le package de modèle.
  2. Décompressez le package.
  3. Déployez à l’aide des ressources décompressées.

* **La prévision du score R2 est toujours égale à zéro** : Ce problème survient si les données d’apprentissage fournies contiennent des séries chronologiques contenant la même valeur pour les `n_cv_splits` + `forecasting_horizon` derniers points de données. Si ce modèle est attendu dans votre série chronologique, vous pouvez faire basculer votre métrique principale vers l'erreur normale de racine quadratique moyenne.
 
* **TensorFlow** : Depuis la version 1.5.0 du Kit de développement logiciel (SDK), le Machine Learning automatisé n’installe pas de modèles TensorFlow par défaut. Pour installer TensorFlow et l’utiliser avec vos expériences de ML automatisé, installez tensorflow==1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```

* **Graphiques d’expérience** : Les graphiques de classification binaire (rappel de précision, ROC, obtenir la courbe, etc.) indiqués dans les itérations d’expériences ML automatisées ne sont pas correctement rendus dans l’interface utilisateur depuis le 12/04. Les tracés de graphique affichent actuellement les résultats inverses, ainsi les modèles plus performants sont affichés avec les résultats les plus bas. Une résolution est en cours d’investigation.

* **Annuler l’exécution d’un machine learning automatisé Databricks** : Si vous utilisez les fonctionnalités de machine learning automatisé sur Azure Databricks et souhaitez annuler une exécution pour en démarrer une nouvelle à des fins d’expérimentation, redémarrez votre cluster Azure Databricks.

* **>10 itérations d’apprentissage automatique automatisé Databricks** : Dans les paramètres de machine learning automatisé, si vous avez plus de 10 itérations, définissez `show_output` sur `False` lorsque vous soumettez l’exécution.

* **Widget Databricks pour le Kit de développement logiciel (SDK) Azure Machine Learning et le Machine Learning automatisé** : Le widget du Kit de développement logiciel (SDK) Azure Machine Learning n’est pas pris en charge dans un notebook Azure Databricks, car les notebooks ne peuvent pas analyser les widgets HTML. Vous pouvez afficher le widget dans le portail à l’aide de ce code Python dans la cellule du notebook Azure Databricks :

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **Échec d’automl_setup** : 
    * Sur Windows, exécutez automl_setup à partir d’une invite Anaconda. Utilisez ce lien pour [installer Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Assurez-vous que Conda 64 bits est installé au lieu de la version 32 bits à l’aide de la commande `conda info`. La `platform` doit être `win-64` pour Windows ou `osx-64` pour Mac.
    * Assurez-vous que Conda 4.4.10 ou une version ultérieure est installé. Vous pouvez vérifier la version à l’aide de la commande `conda -V`. Si une version précédente est installée, vous pouvez la mettre à jour à l’aide de la commande : `conda update conda`.
    * Linux – `gcc: error trying to exec 'cc1plus'`
      *  Si l'erreur `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` est rencontrée, installez le package build-essentials à l'aide de la commande `sudo apt-get install build-essential`.
      * Transmettez un nouveau nom comme premier paramètre à automl_setup pour créer un nouvel environnement Conda. Affichez les environnements Conda existants à l’aide de `conda env list` et supprimez-les avec `conda env remove -n <environmentname>`.
      
* **Échec d’automl_setup_linux.sh** : Si automl_setup_linus.sh échoue sur Ubuntu Linux avec l’erreur : `unable to execute 'gcc': No such file or directory`-
  1. Assurez-vous que les ports de sortie 53 et 80 sont activés. Sur une machine virtuelle Azure, vous pouvez le faire à partir du portail Azure en sélectionnant la machine virtuelle et en cliquant sur Mise en réseau.
  2. Exécutez la commande `sudo apt-get update`
  3. Exécutez la commande `sudo apt-get install build-essential --fix-missing`
  4. Exécutez de nouveau `automl_setup_linux.sh`

* **Échec de configuration.ipynb**  :
  * Pour un environnement Conda local, assurez-vous d'abord qu'automl_setup s'est bien exécutée.
  * Assurez-vous que le paramètre subscription_id est correct. Recherchez le subscription_id dans le portail Azure en sélectionnant Tous les services, puis Abonnements. Les caractères « < » et « > » ne doivent pas être inclus dans la valeur subscription_id. Par exemple, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` a le format valide.
  * Assurez-vous que le Contributeur ou le Propriétaire a accès à l’abonnement.
  * Vérifiez que la région est l’une des régions prises en charge : `eastus2`, `eastus`, `westcentralus`, `southeastasia`, `westeurope`, `australiaeast`, `westus2`, `southcentralus`.
  * Vérifiez l’accès à la région à l’aide du portail Azure.
  
* **`import AutoMLConfig` échoue**  : La version 1.0.76 de Machine Learning automatisé a fait l’objet de modifications qui nécessitent de désinstaller la version précédente avant de passer à la nouvelle version. Si `ImportError: cannot import name AutoMLConfig` est rencontré après une mise à niveau à partir d’une version du Kit de développement logiciel (SDK) antérieure à v1.0.76 vers v1.0.76 ou une version ultérieure, résolvez l’erreur en exécutant : `pip uninstall azureml-train automl` puis `pip install azureml-train-auotml`. Le script automl_setup.cmd le fait automatiquement. 

* **Échec de workspace.from_config** : Si l’appel ws = Workspace.from_config()' échoue -
  1. Assurez-vous que le notebook configuration.ipynb s’est exécuté correctement.
  2. Si le notebook est exécuté à partir d’un dossier qui ne se trouve pas sous le dossier dans lequel `configuration.ipynb` a été exécuté, copiez le dossier aml_config et le fichier config.json qu’il contient dans le nouveau dossier. Workspace.from_config lit le fichier config.json pour le dossier du notebook ou son dossier parent.
  3. Si vous utilisez un nouvel abonnement, un nouveau groupe de ressources, un nouvel espace de travail ou une nouvelle région, veillez à exécuter à nouveau le notebook `configuration.ipynb`. La modification directe de config.json ne fonctionnera que si l’espace de travail existe déjà dans le groupe de ressources spécifié sous l’abonnement spécifié.
  4. Si vous souhaitez modifier la région, modifiez l’espace de travail, le groupe de ressources ou l’abonnement. `Workspace.create` ne crée pas ni ne met à jour un espace de travail s’il existe déjà, même si la région spécifiée est différente.
  
* **Échec de l’exemple de notebook** : Si un exemple de notebook échoue avec une erreur indiquant que la propriété, la méthode ou la bibliothèque n'existe pas :
  * Assurez-vous que le noyau approprié a été sélectionné dans Jupyter Notebook. Le noyau est affiché en haut à droite de la page du notebook. La valeur par défaut est azure_automl. Le noyau est enregistré dans le cadre du notebook. Par conséquent, si vous basculez vers un nouvel environnement Conda, vous devrez sélectionner le nouveau noyau dans le notebook.
      * Pour Azure Notebooks, il doit s’agir de Python 3.6. 
      * Pour les environnements Conda locaux, il doit s'agir du nom de l'environnement Conda que vous avez spécifié dans automl_setup.
  * Assurez-vous que le notebook est destiné à la version du Kit de développement logiciel (SDK) que vous utilisez. Vous pouvez vérifier la version du Kit de développement logiciel (SDK) en exécutant `azureml.core.VERSION` dans une cellule Jupyter Notebook. Vous pouvez télécharger la version précédente des exemples de notebooks à partir de GitHub en cliquant sur le bouton `Branch`, en sélectionnant l’onglet `Tags`, puis en sélectionnant la version.

* **`import numpy` échoue dans Windows** : Certains environnements Windows rencontrent une erreur lors du chargement de NumPy avec la dernière version de Python 3.6.8. Si vous rencontrez ce problème, essayez avec la version 3.6.7 de Python.

* **`import numpy` échoue**  : Vérifiez la version de TensorFlow dans l'environnement Conda de Machine Learning automatisé. Les versions prises en charge sont celles antérieures à la version 1.13. Désinstallez TensorFlow de l’environnement si la version est > = 1.13. Pour vérifier la version de TensorFlow et la désinstaller, procédez comme suit :
  1. Démarrez un interpréteur de commandes, puis activez l’environnement Conda dans lequel les packages de ML automatisé sont installés.
  2. Entrez `pip freeze` et recherchez `tensorflow` : le cas échéant, la version indiquée doit être antérieure à la version 1.13.
  3. Si la version indiquée n’est pas une version prise en charge, exécutez `pip uninstall tensorflow` dans l’interpréteur de commandes et entrez « y » pour confirmer.
  
 * **L’exécution échoue avec `jwt.exceptions.DecodeError`**  : Message d’erreur exact : `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`.

    Pour les versions <= 1.17.0 du kit de développement logiciel (SDK), l'installation peut aboutir à une version non prise en charge de PyJWT. Vérifiez la version de PyJWT dans l'environnement ml conda automatisé. Les versions prises en charge sont celles antérieures à la version 2.0.0. Pour vérifier la version de PyJWT, procédez comme suit :
    1. Démarrez un interpréteur de commandes, puis activez l’environnement Conda dans lequel les packages de ML automatisé sont installés.
    2. Entrez `pip freeze` et recherchez `PyJWT` : le cas échéant, la version indiquée doit être antérieure à la version 2.0.0.

    Si la version indiquée n'est pas prise en charge :
    1. Envisagez une mise à niveau vers la dernière version du kit de développement logiciel (SDK) AutoML : `pip install -U azureml-sdk[automl]`.
    2. Si cela se révèle impossible, désinstallez PyJWT de l'environnement et installez la version appropriée en procédant comme suit :
        - `pip uninstall PyJWT` dans l'interpréteur de commandes et entrez `y` pour confirmer.
        - Installez à l’aide de `pip install 'PyJWT<2.0.0'`.

## <a name="next-steps"></a>Étapes suivantes

+ Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).

+ Découvrez plus d’informations sur [comment entraîner un modèle de régression avec le machine learning automatisé](tutorial-auto-train-models.md) ou [comment entraîner avec le machine learning automatisé sur une ressource distante](how-to-auto-train-remote.md).

+ Découvrez comment effectuer l’apprentissage de plusieurs modèles avec AutoML dans l’article [Many Models Solution Accelerator](https://aka.ms/many-models) (Accélérateur de solution de nombreux modèles).
