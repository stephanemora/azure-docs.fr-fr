---
title: Créer des expériences de machine learning automatisé
titleSuffix: Azure Machine Learning service
description: Le machine learning automatisé choisit un algorithme pour vous et génère un modèle prêt pour le déploiement. Découvrez les options que vous pouvez utiliser pour configurer les expériences de machine learning automatisé.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3dedf5de1ac2c88a9a00fd5f62e0663b840c0fd9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438521"
---
# <a name="configure-automated-machine-learning-experiments"></a>Configurer des expériences de machine learning automatisé

Le machine learning automatisé choisit pour vous un algorithme et des hyperparamètres, et génère un modèle prêt pour le déploiement. Vous disposez de plusieurs options pour configurer les expériences de machine learning automatisé. Dans ce guide, vous découvrez comment définir différents paramètres de configuration.

Pour voir des exemples d’expérience de machine learning automatisé, consultez [Tutoriel : Entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) ou [Entraîner des modèles avec le machine learning automatisé dans le cloud](how-to-auto-train-remote.md).

Options de configuration disponibles dans le machine learning automatisé :

* Sélectionnez votre type d’expérience : Classification, régression ou prévision
* Source de données, formats et récupération de données
* Choisir votre cible de calcul (locale ou distante)
* Paramètres de l’expérience de machine learning automatisé
* Exécuter une expérience de machine learning automatisé
* Explorer les métriques du modèle
* Inscrire et déployer un modèle

## <a name="select-your-experiment-type"></a>Sélectionner le type de votre expérience
Avant de commencer votre expérience, vous devez déterminer le type de problème de machine learning que vous résolvez. Le machine learning automatisé prend en charge les types de tâches de classification, de régression et de prévision. 

Si les fonctionnalités du machine learning automatisé sont en disponibilité générale, **la prévision est encore en préversion publique.**

Le machine learning automatisé prend en charge les algorithmes suivants lors du processus d’automatisation et d’optimisation. En tant qu’utilisateur, vous n’avez pas besoin de spécifier l’algorithme.

classification ; | régression ; | Prévisions
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[C-Support Vector Classification (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


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
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
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

Clé | type |    Mutuellement exclusif avec | Description
---|---|---|---
X | Tramedonnées Pandas ou tableaux Numpy | data_train, étiquette, colonnes |  Toutes les caractéristiques pour l’entraînement
y | Tramedonnées Pandas ou tableaux Numpy |   label   | Données des étiquettes pour l’entraînement. Pour la classification, il doit s’agir d’un tableau d’entiers.
X_valid | Tramedonnées Pandas ou tableaux Numpy   | data_train, étiquette | _Facultatif_ Toutes les caractéristiques pour la validation. Si non spécifié, X est divisé entre l’entraînement et la validation
y_valid |   Tramedonnées Pandas ou tableaux Numpy | data_train, étiquette | _Facultatif_ Les données d’étiquette pour l’entraînement. Si non spécifié, y est divisé entre l’entraînement et la validation
sample_weight | Tramedonnées Pandas ou tableaux Numpy |   data_train, étiquette, colonnes| _Facultatif_ Une valeur de pondération pour chaque échantillon. À utiliser quand vous voulez affecter des pondérations différentes à vos points de données 
sample_weight_valid | Tramedonnées Pandas ou tableaux Numpy | data_train, étiquette, colonnes |    _Facultatif_ Une valeur de pondération pour chaque échantillon de validation. Si non spécifié, sample_weight est divisé entre l’entraînement et la validation
data_train |    Tramedonnées Pandas |  X, y, X_valid, y_valid |    Toutes les données (caractéristiques + étiquette) pour l’entraînement
label | chaîne  | X, y, X_valid, y_valid |  La colonne de data_train qui représente l’étiquette
colonnes | Tableau de chaînes  ||  _Facultatif_ Liste verte de colonnes à utiliser pour les caractéristiques
cv_splits_indices   | Tableau d’entiers ||  _Facultatif_ Liste des index pour diviser les données pour la validation croisée

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Charger et préparer des données avec le SDK DataPrep
Les expériences de machine learning automatisé prennent en charge le chargement et les transformations des données avec le SDK DataPrep. L’utilisation du SDK donne les possibilités suivantes :

>* Chargement à partir de nombreux types de fichiers avec déduction des paramètres d’analyse (codage, séparateur, en-têtes)
>* Conversion de type à l’aide de l’inférence pendant le chargement de fichier
>* Prise en charge de la connexion pour MS SQL Server et Azure Data Lake Storage
>* Ajouter une colonne à l’aide d’une expression
>* Imputer des valeurs manquantes
>* Dériver des colonnes par exemple
>* Filtrage
>* Transformations Python personnalisées

Pour plus d’informations sur le SDK DataPrep, reportez-vous à l’article [Guide pratique pour préparer les données pour la modélisation](how-to-load-data.md). Voici un exemple de chargement de données avec le SDK DataPrep. 
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Données pour l’entraînement et la validation

Vous pouvez spécifier des jeux de données distincts pour l’entraînement et la validation via get_data() ou directement dans la méthode `AutoMLConfig`.

## <a name="cross-validation-split-options"></a>Options de division de la validation croisée

### <a name="k-folds-cross-validation"></a>Validation croisée K-Folds

Utilisez le paramètre `n_cross_validations` pour spécifier le nombre de validations croisées. Le jeu de données d’entraînement est divisé en `n_cross_validations` tranches de taille égale. Lors de chaque passe de validation croisée, une des tranches est utilisée pour la validation du modèle entraîné sur les tranches restantes. Ce processus se répète pour `n_cross_validations` passes jusqu’à ce que chaque tranche soit utilisée une fois comme jeu de validation. Les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Validation croisée Monte Carlo (Sous-échantillonnage aléatoire répété)

Utilisez `validation_size` pour spécifier le pourcentage du jeu de données d’entraînement qui doit être utilisé pour la validation, et utilisez `n_cross_validations` pour spécifier le nombre de validations croisées. Lors de chaque passe de validation croisée, un sous-ensemble de taille `validation_size` est sélectionné de façon aléatoire pour la validation du modèle entraîné sur les données restantes. Enfin, les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement.

### <a name="custom-validation-dataset"></a>Jeu de données de validation personnalisé

Utilisez un jeu de données de validation personnalisé si la division aléatoire n’est pas acceptable (en général, des données de séries chronologiques ou des données déséquilibrées). Vous pouvez spécifier votre propre jeu de données de validation. Le modèle est alors évalué par rapport au jeu de données de validation spécifié au lieu du jeu de données aléatoire.

## <a name="compute-to-run-experiment"></a>Capacité de calcul pour exécuter l’expérience

Ensuite, l’endroit où le modèle doit être entraîné est déterminé. Une expérience de machine learning automatisé peut s’exécuter sur les options de calcul suivantes :
*   Votre machine locale, comme un poste de travail local ou un ordinateur portable : en général, quand vous avez un petit jeu de données et que vous êtes toujours dans la phase d’exploration.
*   Une machine distante dans le cloud : la [capacité de calcul managée Azure Machine Learning](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) est un service managé qui permet d’entraîner des modèles de machine learning sur des clusters de machines virtuelles Azure.

Consultez le [site GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl) pour obtenir des exemples de notebooks avec des cibles de calcul locales et distantes.

<a name='configure-experiment'/>

## <a name="configure-your-experiment-settings"></a>Configurer les paramètres de votre expérience

Vous disposez de plusieurs options pour configurer votre expérience de machine learning automatisé. Ces paramètres sont définis en instanciant un objet `AutoMLConfig`.

Voici quelques exemples :

1.  Expérience de classification avec AUC pondéré comme métrique principale avec une durée maximale de 12 000 secondes par itération, avec l’expérience se terminant au bout de 50 itérations et 2 tranches de validation croisée.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Voici un exemple d’une expérience de régression définie pour se terminer au bout de 100 itérations, avec chaque itération durant jusqu’à 600 secondes avec 5 tranches de validation croisée.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Ce tableau liste les paramètres disponibles pour votre expérience et leur valeur par défaut.

Propriété |  Description | Valeur par défaut
--|--|--
`task`  |Spécifiez le type de problème de machine learning. Les valeurs autorisées sont <li>classification ;</li><li>régression ;</li><li>Prévisions</li>    | Aucun |
`primary_metric` |Métrique que vous voulez optimiser pour la création de votre modèle. Par exemple, si vous spécifiez la précision comme primary_metric, le machine learning automatisé recherche un modèle avec la précision maximale. Vous pouvez spécifier seulement un élément primary_metric par expérience. Les valeurs autorisées sont <br/>**Classification** :<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Régression** : <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score  </li> | Pour la classification : accuracy <br/>Pour la régression : spearman_correlation <br/> |
`experiment_exit_score` |   Vous pouvez définir une valeur cible pour votre primary_metric. Une fois qu’un modèle correspondant à la primary_metric cible est trouvé, le machine learning automatisé arrête les itérations et l’expérience se termine. Si cette valeur n’est pas définie (l’option par défaut), l’expérience de machine learning automatisé continue de s’exécuter jusqu’à atteindre le nombre d’itérations spécifié dans Itérations. Prend une valeur double. Si la cible n’est jamais atteinte, le machine learning automatisé continue jusqu’à atteindre le nombre d’itérations spécifié dans Itérations.| Aucun
`iterations` |Nombre maximal d’itérations. Chaque itération correspond à un travail d’entraînement dont les résultats produisent un pipeline. Un pipeline correspond à un prétraitement des données et à un modèle. Pour obtenir un modèle de haute qualité, utilisez 250 ou plus    | 100
`max_concurrent_iterations`|    Nombre maximal d’itérations à exécuter en parallèle. Ce paramètre fonctionne seulement pour une capacité de calcul distante.|   1
`max_cores_per_iteration`   | Indique le nombre de cœurs sur la cible de calcul à utiliser pour entraîner un seul pipeline. Si l’algorithme peut tirer parti de plusieurs cœurs, cela augmente les performances sur une machine multicœur. Vous pouvez le définir sur -1 pour utiliser tous les cœurs disponibles sur la machine.|  1
`iteration_timeout_minutes` |   Limite la quantité de temps (en minutes) d’une itération particulière. Si une itération dépasse la quantité spécifiée, cette itération est annulée. Si ce paramètre n’est pas défini, l’itération continue de s’exécuter jusqu’à ce qu’elle se termine. |   Aucun
`n_cross_validations`   |Nombre de divisions pour la validation croisée.| Aucun
`validation_size`   |Taille du jeu de validation définie en pourcentage de tous les échantillons de l’entraînement.|  Aucun
`preprocess` | True/False <br/>True permet à l’expérience d’effectuer un prétraitement sur l’entrée. Voici un sous-ensemble de prétraitement<li>Données manquantes : Attribue des valeurs aux données manquantes – la moyenne pour les données numériques, la chaîne ayant le plus d’occurrences pour du texte </li><li>Données catégorielles : Si le type de données est numérique et que le nombre de valeurs uniques est inférieur à 5 pour cent, convertit en un encodage one-hot </li><li>Pour obtenir la liste complète, consultez [le dépôt GitHub](https://aka.ms/aml-notebooks)</li><br/>Remarque : Si les données sont éparses, vous ne pouvez pas utiliser preprocess = true |  False | 
`blacklist_models`  | L’expérience de machine learning automatisé fait des essais avec de nombreux algorithmes différents. Configurez pour exclure certains algorithmes de l’expérience. Pratique si vous savez qu’un ou plusieurs algorithmes spécifiques ne fonctionnent pas bien pour votre jeu de données. Exclure des algorithmes peut économiser des ressources de calcul et du temps d’entraînement.<br/>Valeurs autorisées pour la classification<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Valeurs autorisées pour la régression<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Valeurs autorisées pour la prévision<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|   Aucun
`whitelist_models`  | L’expérience de machine learning automatisé fait des essais avec de nombreux algorithmes différents. Configurez pour inclure certains algorithmes pour l’expérience. Pratique si vous savez qu’un ou plusieurs algorithmes spécifiques fonctionnent bien pour votre jeu de données. <br/>Valeurs autorisées pour la classification<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Valeurs autorisées pour la régression<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Valeurs autorisées pour la prévision<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|  Aucun
`verbosity` |Contrôle le niveau de journalisation, INFO étant le plus détaillé, CRITICAL étant le moins détaillé. Le niveau de détail prend les mêmes valeurs que celles définies dans le package de journalisation de Python. Les valeurs autorisées sont les suivantes :<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | Toutes les caractéristiques pour l’entraînement |  Aucun
`y` |   Données des étiquettes pour l’entraînement. Pour la classification, il doit s’agir d’un tableau d’entiers.|  Aucun
`X_valid`|_Facultatif_ Toutes les caractéristiques pour la validation. Si non spécifié, X est divisé entre l’entraînement et la validation |   Aucun
`y_valid`   |_Facultatif_ Les données d’étiquette pour l’entraînement. Si non spécifié, y est divisé entre l’entraînement et la validation    | Aucun
`sample_weight` |   _Facultatif_ Une valeur de pondération pour chaque échantillon. À utiliser quand vous voulez affecter des pondérations différentes à vos points de données |   Aucun
`sample_weight_valid`   |   _Facultatif_ Une valeur de pondération pour chaque échantillon de validation. Si la valeur n’est pas spécifiée, sample_weight est divisé entre l’entraînement et la validation   | Aucun
`run_configuration` |   Objet RunConfiguration.  Utilisé pour les exécutions à distance. |Aucun
`data_script`  |    Chemin d’un fichier contenant la méthode get_data.  Obligatoire pour les exécutions à distance.   |Aucun
`model_explainability` | _Facultatif_ True/False <br/>  True permet à l’expérience de d’utiliser la fonctionnalité « Importance de la caractéristique » pour chaque itération. Vous pouvez également utiliser la méthode explain_model() sur une itération spécifique pour activer la fonctionnalité « Importance de la caractéristique » à la demande pour cette itération une fois l’expérience terminée. | False
`enable_ensembling`|Indicateur pour activer une itération sur l’ensemble une fois que toutes les autres itérations sont terminées.| True 
`ensemble_iterations`|Nombre d’itérations au cours desquelles nous choisissons un pipeline adapté comme faisant partie de l’ensemble final.| 15
`experiment_timeout_minutes`| Limite la quantité de temps (minutes) que peut prendre l’exécution de toute l’expérience | Aucun

## <a name="data-pre-processing-and-featurization"></a>Prétraitement et caractérisation des données

Si vous utilisez `preprocess=True`, les étapes de prétraitement des données suivantes sont effectuées automatiquement pour vous :
1.  Suppression des cardinalités élevées ou aucune caractéristique de variance
    * Suppression des caractéristiques sans informations utiles dans les jeux d’entraînement et de validation. Il s’agit des caractéristiques dont toutes les valeurs sont manquantes, ayant la même valeur dans toutes les lignes ou présentant une cardinalité très élevée (par exemple des hachages, des ID ou des GUID).
1.  Imputation des valeurs manquantes
    *   Pour les caractéristiques numériques, remplacement des valeurs manquantes par la moyenne des valeurs dans la colonne.
    *   Pour les caractéristiques catégorielles, remplacement des valeurs manquantes par la valeur la plus fréquente.
1.  Générer des caractéristiques supplémentaires
    * Pour les caractéristiques de type date/heure : Année, Mois, Jour, Jour de la semaine, Jour de l’année, Trimestre, Semaine de l’année, Heure, Minute, Seconde.
    * Pour les caractéristiques de type texte : Fréquence des termes basée sur les unigrammes, les bigrammes et les trigrammes des mots, vectoriseur par comptage (Count vectorizer).
1.  Transformations et encodages
    * Caractéristiques numériques avec très peu de valeurs uniques transformées en caractéristiques catégorielles.
    * En fonction de la cardinalité des caractéristiques catégorielles, encodage d’étiquettes ou encodage one-hot (hachage).

## <a name="run-experiment"></a>Exécuter une expérience

Soumettez l’expérience pour qu’elle s’exécute et génère un modèle. Passez `AutoMLConfig` à la méthode `submit` pour générer le modèle.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Les dépendances sont d’abord installées sur une nouvelle machine.  Jusqu’à 10 minutes peuvent être nécessaires avant l’affichage de la sortie.
>La définition de `show_output` sur `True` fait que la sortie est affichée sur la console.


## <a name="explore-model-metrics"></a>Explorer les métriques du modèle
Vous pouvez consulter vos résultats dans un widget ou en ligne si vous êtes dans un notebook. Pour plus d’informations, consultez [Suivre et évaluer des modèles](how-to-track-experiments.md#view-run-details).


### <a name="classification-metrics"></a>Métriques de classification
Les métriques suivantes sont enregistrées dans chaque itération pour une tâche de classification.

|Métrique principale|Description|Calcul|Paramètres supplémentaires
--|--|--|--|--|
AUC_Macro| « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Macro » est la moyenne arithmétique de l’AUC pour chaque classe.  | [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Micro » est calculé globalement en combinant les vrais positifs et les faux positifs de chaque classe| [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Weighted » est la moyenne arithmétique du score pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.| [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|La précision est le pourcentage d’étiquettes prédites qui correspondent exactement aux étiquettes réelles. |[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Aucun|
average_precision_score_macro|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. Macro est la moyenne arithmétique du score de précision moyen de chaque classe|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. « Micro » est calculé globalement en combinant les vrais positifs et les faux positifs de chaque limite|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. « Weighted » est la moyenne arithmétique du score de précision moyen pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|La précision équilibrée est la moyenne arithmétique du rappel pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Le score F1 est la moyenne harmonique de la précision et du rappel. « Macro » est la moyenne arithmétique du score F1 pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Le score F1 est la moyenne harmonique de la précision et du rappel. « Micro » est calculé globalement en comptant le total des vrais positifs, des faux négatifs et des faux positifs|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Le score F1 est la moyenne harmonique de la précision et du rappel. Moyenne pondérée par fréquence de classe du score F1 pour chaque classe|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Il s’agit de la fonction de perte utilisée dans la régression logistique (multinomiale) et les extensions de celle-ci, comme les réseaux neuronaux, définie comme la probabilité logarithmique négative des étiquettes réelles, étant données les prédictions d’un classifieur probabiliste. Pour un échantillon unique avec l’étiquette vraie yt dans {0,1} et la probabilité estimée yp que yt = 1, la perte logarithmique est - -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Aucun|
norm_macro_recall|Le rappel macro normalisé est tel que la performance aléatoire ait un score de 0 et la performance parfaite ait un score de 1. Ceci est réalisé par norm_macro_recall := (recall_score_macro - R)/(1 - R), où R est la valeur attendue de recall_score_macro pour des prédictions aléatoires (c’est-à-dire R=0,5 pour la classification binaire) et R=(1/C) pour les problèmes de classification de classe C)|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro", puis norm_macro_recall := (recall_score_macro - R)/(1 - R), où R est la valeur attendue de recall_score_macro pour des prédictions aléatoires (c’est-à-dire R=0,5 pour la classification binaire) et R=(1/C) pour les problèmes de classification de classe C)|
precision_score_macro|La précision est le pourcentage d’éléments étiquetés comme classe certaine et qui sont réellement dans cette classe. « Macro » est la moyenne arithmétique de la précision pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|La précision est le pourcentage d’éléments étiquetés comme classe certaine et qui sont réellement dans cette classe. « Micro » est calculé globalement en comptant le total des vrais positifs et des faux positifs|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|La précision est le pourcentage d’éléments étiquetés comme classe certaine et qui sont réellement dans cette classe. « Weighted » est la moyenne arithmétique de la précision pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Le rappel est le pourcentage d’éléments qui sont réellement dans une classe certaine et qui sont correctement étiquetés. « Macro » est la moyenne arithmétique du rappel pour chaque classe|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Le rappel est le pourcentage d’éléments qui sont réellement dans une classe certaine et qui sont correctement étiquetés. « Micro » est calculé globalement en comptant le total des vrais positifs et des faux négatifs|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Le rappel est le pourcentage d’éléments qui sont réellement dans une classe certaine et qui sont correctement étiquetés. « Weighted » est la moyenne arithmétique du rappel pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|La précision pondérée est la précision où le poids donné à chaque exemple est égal à la proportion d’instances « true » dans la classe « true » de cet exemple|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight est un vecteur égal à la proportion de cette classe pour chaque élément dans la cible|

### <a name="regression-and-forecasting-metrics"></a>Métriques de régression et de prévision
Les métriques suivantes sont enregistrées dans chaque itération pour une tâche de régression ou de prévision.

|Métrique principale|Description|Calcul|Paramètres supplémentaires
--|--|--|--|--|
explained_variance|La variance expliquée est la proportion selon laquelle un modèle mathématique compte pour la variation d’un jeu de données particulier. C’est le pourcentage de diminution de la variance des données d’origine par rapport à la variance des erreurs. Quand la moyenne des erreurs est 0, elle est égale à la variance expliquée.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Aucun|
r2_score|R2 est le coefficient de détermination ou le pourcentage de réduction dans les erreurs quadratiques comparé à un modèle de référence qui génère la moyenne. Quand la moyenne des erreurs est 0, elle est égale à la variance expliquée.|[Calcul](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Aucun|
spearman_correlation|La corrélation de Spearman est une mesure non paramétrique de la monotonie de la relation entre deux jeux de données. Contrairement à la corrélation de Pearson, la corrélation de Spearman ne suppose pas que les deux jeux de données sont normalement distribués. Comme d’autres coefficients de corrélation, celle-ci varie entre -1 et +1, 0 impliquant l’absence de corrélation. Les corrélations de -1 ou +1 impliquent une relation monotone exacte. Les corrélations positives impliquent que quand x augmente, y augmente également. Les corrélations négatives impliquent que quand x augmente, y diminue.|[Calcul](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Aucun|
mean_absolute_error|L’erreur d’absolue moyenne est la valeur attendue de la valeur absolue de la différence entre la cible et la prédiction|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Aucun|
normalized_mean_absolute_error|L’erreur d’absolue moyenne normalisée est l’erreur absolue moyenne divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Diviser par la plage de données|
median_absolute_error|L’erreur absolue médiane est la médiane de toutes les différences absolues entre la cible et la prédiction. Cette perte est robuste pour les valeurs hors norme.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Aucun|
normalized_median_absolute_error|L’erreur d’absolue médiane normalisée est l’erreur absolue médiane divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Diviser par la plage de données|
root_mean_squared_error|L’erreur quadratique moyenne racine est la racine carrée de la différence quadratique attendue entre la cible et la prédiction|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Aucun|
normalized_root_mean_squared_error|L’erreur quadratique moyenne racine normalisée est l’erreur quadratique moyenne racine divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Diviser par la plage de données|
root_mean_squared_log_error|L’erreur logarithmique quadratique moyenne racine est la racine carrée de l’erreur logarithmique quadratique attendue|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Aucun|
normalized_root_mean_squared_log_error|L’erreur logarithmique quadratique moyenne racine normalisée est l’erreur logarithmique quadratique moyenne racine divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Diviser par la plage de données|

## <a name="explain-the-model"></a>Expliquer le modèle

Si les fonctionnalités du machine learning automatisé sont en disponibilité générale, **les fonctionnalités d’explicabilité des modèles sont encore en préversion publique.**

Le machine learning automatisé vous permet de comprendre l’importance des caractéristiques.  Pendant le processus d’entraînement, vous pouvez obtenir l’importance globale des caractéristiques pour le modèle.  Pour les scénarios de classification, vous pouvez également obtenir d’importance des caractéristiques au niveau des classes.  Vous devez fournir un jeu de données de validation (X_valid) pour obtenir l’importance des caractéristiques.

Il y a deux façons de générer l’importance des caractéristiques.

*   Une fois qu’une expérience est terminée, vous pouvez utiliser la méthode `explain_model` sur une des itérations.

    ```
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

    ```
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

    ```
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

Vous pouvez visualiser le graphique d’importance des caractéristiques dans votre espace de travail du portail Azure. Le graphique apparaît également quand vous utilisez le widget Jupyter dans un notebook. Pour plus d’informations sur les graphiques, consultez l’article [Exemples de notebooks Azure ML.](samples-notebooks.md)

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Graphique d’importance des caractéristiques](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).

Découvrez plus d’informations sur [comment entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) ou [comment entraîner avec le machine learning automatisé sur une ressource distante](how-to-auto-train-remote.md). 
