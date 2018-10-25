---
title: Configurer votre expérience de machine learning automatisé - Azure Machine Learning
description: Le machine learning automatisé choisit un algorithme pour vous et génère un modèle prêt pour le déploiement. Découvrez les options que vous pouvez utiliser pour configurer les expériences de machine learning automatisé.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1aeb1315cfafbcdf3507a6e49d71e1f1e69b537c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430185"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>Configurer votre expérience de machine learning automatisé

Le machine learning automatisé choisit un algorithme et des hyperparamètres pour vous, et génère un modèle prêt pour le déploiement. Vous pouvez télécharger le modèle pour le personnaliser. Vous pouvez utiliser plusieurs options pour configurer des expériences de machine learning automatisé. Dans ce guide, vous allez apprendre à définir différents paramètres de configuration.

Pour voir des exemples de machine learning automatisé, consultez [Tutoriel : Entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) ou [Entraîner des modèles avec le machine learning automatisé dans le cloud](how-to-auto-train-remote.md).

Options de configuration disponibles dans le machine learning automatisé :

* Sélectionner votre expérience, par exemple Classification ou Régression 
* Source de données, formats et récupération de données
* Choisir votre cible de calcul (locale ou distante)
* Paramètres de l’expérience de machine learning automatisé
* Exécuter une expérience de machine learning automatisé
* Explorer les métriques du modèle
* Inscrire et déployer un modèle

## <a name="select-your-experiment-type"></a>Sélectionner le type de votre expérience
Avant de commencer votre expérience, vous devez déterminer le type de problème de machine learning que vous résolvez. Le machine learning automatisé prend en charge deux catégories d’apprentissage supervisé : Classification et Régression. Le machine learning automatisé prend en charge les algorithmes suivants lors du processus d’automatisation et d’optimisation. En tant qu’utilisateur, vous n’avez pas besoin de spécifier l’algorithme.
classification ; | régression ;
--|--
sklearn.linear_model.LogisticRegression | sklearn.linear_model.ElasticNet
sklearn.linear_model.SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes.BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes.MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model.LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model.SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm.LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm.LGBMClassifier |


## <a name="data-source-and-format"></a>Source et format des données
Le machine learning automatisé prend en charge les données qui se trouvent sur votre poste de travail local ou dans le cloud dans Stockage Blob Azure. Les données peuvent être lues dans les formats de données pris en charge par scikit-learn. Vous pouvez lire les données dans 1) des tableaux Numpy X (caractéristiques) et y (variable cible ou également appelé « étiquette ») ou dans 2) des tramedonnées Pandas. 

Exemples :

1.  Tableaux Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Tramedonnées Pandas

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Récupérer des données pour exécuter une expérience sur une capacité de calcul distante

Si vous utilisez une capacité de calcul distante pour exécuter votre expérience, l’extraction de données doit être encapsulée dans un script Python distinct `get_data()`. Ce script est exécuté sur la capacité de calcul distante où l’expérience de machine learning automatisé est exécutée. `get_data` élimine le besoin d’extraire les données sur le réseau pour chaque itération. Sans `get_data`, votre expérience échoue si vous l’exécutez sur une capacité de calcul distante.

Voici un exemple de `get_data` :

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

Le script `get_data` peut retourner ceci :
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

## <a name="train-and-validation-data"></a>Données pour l’entraînement et la validation

Vous pouvez spécifier des jeux de données distincts pour l’entraînement et la validation via get_data() ou directement dans la méthode `AutoMLConfig`.

## <a name="cross-validation-split-options"></a>Options de division de la validation croisée

### <a name="k-folds-cross-validation"></a>Validation croisée K-Folds

Utilisez le paramètre `n_cross_validations` pour spécifier le nombre de validations croisées. Le jeu de données d’entraînement est divisé en `n_cross_validations` tranches de taille égale. Lors de chaque passe de validation croisée, une des tranches est utilisée pour la validation du modèle entraîné sur les tranches restantes. Ce processus se répète pour `n_cross_validations` passes jusqu’à ce que chaque tranche soit utilisée une fois comme jeu de validation. Enfin, les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Validation croisée Monte Carlo (Sous-échantillonnage aléatoire répété)

Utilisez `validation_size` pour spécifier le pourcentage du jeu de données d’entraînement qui doit être utilisé pour la validation, et utilisez `n_cross_validations` pour spécifier le nombre de validations croisées. Lors de chaque passe de validation croisée, un sous-ensemble de taille `validation_size` est sélectionné de façon aléatoire pour la validation du modèle entraîné sur les données restantes. Enfin, les scores moyens sur l’ensemble des `n_cross_validations` passes sont signalés, et le modèle correspondant est réentraîné sur l’ensemble du jeu de données d’entraînement.

### <a name="custom-validation-dataset"></a>Jeu de données de validation personnalisé

Utilisez un jeu de données de validation personnalisé si la division aléatoire n’est pas acceptable (en général, des données de séries chronologiques ou des données déséquilibrées). Avec ceci, vous pouvez spécifier votre propre jeu de données de validation. Le modèle est alors évalué par rapport au jeu de données de validation spécifié au lieu du jeu de données aléatoire.

## <a name="compute-to-run-experiment"></a>Capacité de calcul pour exécuter l’expérience

Ensuite, l’endroit où le modèle doit être entraîné est déterminé. Une expérience d’entraînement de machine learning automatisé s’exécute sur une cible de calcul dont vous êtes propriétaire et que vous gérez. 

Les options de calcul prises en charge sont :
1.  Votre machine locale, comme un poste de travail local ou un ordinateur portable : en général, quand vous avez un petit jeu de données et que vous êtes toujours dans la phase d’exploration.
2.  Une machine distante dans le cloud - [une machine virtuelle Azure Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) exécutant Linux : vous avez un grand jeu de données et vous voulez monter en puissance en passant à une machine de grande taille disponible dans le cloud Azure. 
3.  Cluster Azure Batch AI – Un cluster managé que vous pouvez configurer pour monter en charge et exécuter des itérations de machine learning automatisé en parallèle. 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>Configurer les paramètres de votre expérience

Vous pouvez utiliser plusieurs boutons pour configurer vos expériences de machine learning automatisé. Ces paramètres sont définis en instanciant un objet `AutoMLConfig`.

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
`task`  |Spécifiez le type de problème de machine learning. Les valeurs autorisées sont <li>classification ;</li><li>régression ;</li>    | Aucun |
`primary_metric` |Métrique que vous voulez optimiser pour la création de votre modèle. Par exemple, si vous spécifiez la précision comme primary_metric, le machine learning automatisé recherche un modèle avec la précision maximale. Vous pouvez spécifier seulement un élément primary_metric par expérience. Les valeurs autorisées sont <br/>**Classification** :<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Régression** : <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | Pour la classification : accuracy <br/>Pour la régression : spearman_correlation <br/> |
`exit_score` |  Vous pouvez définir une valeur cible pour votre primary_metric. Une fois qu’un modèle correspondant à la primary_metric cible est trouvé, le machine learning automatisé arrête les itérations et l’expérience se termine. Si cette valeur n’est pas définie (l’option par défaut), expérience de machine learning automatisé continue de s’exécuter jusqu’à atteindre le nombre d’itérations spécifié dans Itérations. Prend une valeur double. Si la cible n’est jamais atteinte, le machine learning automatisé continuera jusqu’à atteindre le nombre d’itérations spécifié dans Itérations.|   Aucun
`iterations` |Nombre maximal d’itérations. Chaque itération correspond à un travail d’entraînement dont les résultats produisent un pipeline. Un pipeline correspond à un prétraitement des données et à un modèle. Pour obtenir un modèle de haute qualité, utilisez 250 ou plus | 100
`Concurrent_iterations`|    Nombre maximal d’itérations à exécuter en parallèle. Ce paramètre fonctionne seulement pour une capacité de calcul distante.|    1
`max_cores_per_iteration`   | Indique le nombre de cœurs sur la cible de calcul à utiliser pour entraîner un seul pipeline. Si l’algorithme peut tirer parti de plusieurs cœurs, cela augmente les performances sur une machine multicœur. Vous pouvez le définir sur -1 pour utiliser tous les cœurs disponibles sur la machine.|  1
`max_time_sec` |    Limite la quantité de temps (en secondes) d’une itération particulière. Si une itération dépasse la quantité spécifiée, cette itération est annulée. Si ce paramètre n’est pas défini, l’itération continue de s’exécuter jusqu’à ce qu’elle se termine. |   Aucun
`n_cross_validations`   |Nombre de divisions pour la validation croisée.| Aucun
`validation_size`   |Taille du jeu de validation définie en pourcentage de tous les échantillons de l’entraînement.|  Aucun
`preprocess` | True/False <br/>True permet à l’expérience d’effectuer un prétraitement sur l’entrée. Voici un sous-ensemble de prétraitement<li>Données manquantes : Attribue des valeurs aux données manquantes - la moyenne pour les données numériques, la chaîne ayant le plus d’occurrences pour le texte </li><li>Valeurs des catégories : Si le type de données est numérique et que le nombre de valeurs uniques est inférieur à 5 pour cent, convertit en un encodage one-hot </li><li>Pour obtenir la liste complète, consultez [le dépôt GitHub](https://aka.ms/aml-notebooks)</li><br/>Remarque : Si les données sont éparses, vous ne pouvez pas utiliser preprocess = true |  False | 
`blacklist_algos`   | L’expérience de machine learning automatisé fait des essais avec de nombreux algorithmes différents. Configurez le machine learning automatisé pour exclure certains algorithmes de l’expérience. Pratique si vous savez qu’un ou plusieurs algorithmes spécifiques ne fonctionnent pas bien pour votre jeu de données. Exclure des algorithmes peut économiser des ressources de calcul et du temps d’entraînement.<br/>Valeurs autorisées pour la classification<br/><li>Régression logique</li><li>Classifieur SGD</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>Arborescences supplémentaires</li><li>Boosting de gradient</li><li>lgbm_classifier</li><br/>Valeurs autorisées pour la régression<br/><li>Réseau élastique</li><li>Régresseur de boosting de gradient</li><li>Régresseur DT</li><li>Régresseur kNN</li><li>LARS LASSO</li><li>Régresseur SGD</li><li>Régresseur RF</li><li>Régresseur de hiérarchies supplémentaires</li>|   Aucun
`verbosity` |Contrôle le niveau de journalisation, INFO étant le plus détaillé, CRITICAL étant le moins détaillé.<br/>Les valeurs autorisées sont les suivantes :<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | Toutes les caractéristiques pour l’entraînement |  Aucun
`y` |   Données des étiquettes pour l’entraînement. Pour la classification, il doit s’agir d’un tableau d’entiers.|  Aucun
`X_valid`|_Facultatif_ Toutes les caractéristiques pour la validation. Si non spécifié, X est divisé entre l’entraînement et la validation |   Aucun
`y_valid`   |_Facultatif_ Les données d’étiquette pour l’entraînement. Si non spécifié, y est divisé entre l’entraînement et la validation    | Aucun
`sample_weight` |   _Facultatif_ Une valeur de pondération pour chaque échantillon. À utiliser quand vous voulez affecter des pondérations différentes à vos points de données |   Aucun
`sample_weight_valid`   |   _Facultatif_ Une valeur de pondération pour chaque échantillon de validation. Si la valeur n’est pas spécifiée, sample_weight est divisé entre l’entraînement et la validation   | Aucun
`run_configuration` |   Objet RunConfiguration.  Utilisé pour les exécutions à distance. |Aucun
`data_script`  |    Chemin d’un fichier contenant la méthode get_data.  Obligatoire pour les exécutions à distance.   |Aucun


## <a name="run-experiment"></a>Exécuter une expérience

Nous pouvons ensuite lancer l’expérience pour qu’elle exécute et génère un modèle pour nous. Passez `AutoMLConfig` à la méthode `submit` pour générer le modèle.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Les dépendances sont d’abord installées sur une nouvelle machine virtuelle DSVM.  Jusqu’à 10 minutes peuvent être nécessaires avant l’affichage de la sortie.
>La définition de `show_output` sur True fait que la sortie est affichée sur la console.


## <a name="explore-model-metrics"></a>Explorer les métriques du modèle
Vous pouvez consulter vos résultats dans un widget ou en ligne si vous êtes dans un notebook. Consultez les détails pour « Suivre et évaluer des modèles ». (Vérifiez que le contenu du machine learning automatisé contient des informations pertinentes)

Les métriques suivantes sont enregistrées dans chaque itération
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).

Découvrez plus d’informations sur [comment entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) ou [comment entraîner avec le machine learning automatisé sur une ressource distante](how-to-auto-train-remote.md). 