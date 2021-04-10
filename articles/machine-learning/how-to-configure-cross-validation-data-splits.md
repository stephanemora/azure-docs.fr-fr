---
title: Fractionnements de données et validation croisée dans le Machine Learning automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment configurer les fractionnements de jeux de données et la validation croisée pour les expériences de Machine Learning automatisé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 31d3dc2c2d8194541ba1fe7d0865e6c939d75f73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102501578"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Configurer les fractionnements de données et la validation croisée dans les opérations de Machine Learning automatisé

Dans cet article, vous allez découvrir les différentes options de configuration des fractionnements de données de formation et de validation et des paramètres de la validation croisée pour vos expériences de Machine Learning automatisé (AutoML).

Dans Azure Machine Learning, quand vous utilisez AutoML pour générer plusieurs modèles de ML, chaque exécution enfant doit valider le modèle associé en calculant les métriques de qualité pour ce modèle, telles que la précision ou la pondération AUC. Ces métriques sont calculées en comparant les prédictions effectuées avec chaque modèle avec des balises d’observations passées basées sur les données de validation. [En savoir plus sur le mode de calcul des métriques en fonction du type de validation](#metric-calculation-for-cross-validation-in-machine-learning). 

Les expériences d’AutoML effectuent automatiquement la validation du modèle. Les sections suivantes décrivent comment vous pouvez personnaliser davantage les paramètres de validation avec le [Kit de développement logiciel (SDK) Python pour Azure Machine Learning](/python/api/overview/azure/ml/). 

Pour une expérience avec peu, voire sans code, consultez la section [Créer vos expériences de machine learning automatisé dans Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

> [!NOTE]
> Studio prend actuellement en charge les fractionnements de données de formation et de validation ainsi que les options de validation croisée, mais il ne prend pas en charge la spécification de fichiers de données individuels pour votre jeu de validation. 

## <a name="prerequisites"></a>Prérequis

Pour cet article, vous avez besoin des éléments suivants :

* Un espace de travail Azure Machine Learning. Pour créer l’espace de travail, voir [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Une connaissance de base en matière de configuration d’une expérience de Machine Learning automatisé avec le Kit de développement logiciel (SDK) pour Azure Machine Learning. Suivez le [didacticiel](tutorial-auto-train-models.md) ou les [procédures](how-to-configure-auto-train.md) pour afficher les modèles de conception fondamentaux des expériences de Machine Learning automatisé.

* Une compréhension des fractionnements de données de formation et de validation et de la validation croisée en tant que concepts de Machine Learning. Pour obtenir une explication plus poussée,

    * [À propos des données de formation, de validation et de test dans le Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Comprendre la validation croisée dans le Machine Learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Fractionnements de données et validation croisée par défaut dans le Machine Learning

Utilisez l’objet [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) pour définir vos paramètres d’expérimentation et de formation. Dans l’extrait de code suivant, Notez que seuls les paramètres requis sont définis, c’est-à-dire que les paramètres pour `n_cross_validation` ou `validation_ data` ne sont **pas** inclus.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Si vous ne spécifiez pas explicitement un paramètre `validation_data` ou `n_cross_validation`, AutoML applique les techniques par défaut en fonction du nombre de lignes fournies dans le jeu de données unique `training_data` :

|Formation sur la taille des&nbsp;données&nbsp;| Technique de validation |
|---|-----|
|**Contient plus&nbsp;de&nbsp;20 000&nbsp;lignes**| Le fractionnement des données de formation/validation est appliqué. La valeur par défaut consiste à prendre 10 % du jeu de données d’apprentissage initial en tant que jeu de validation. Ce jeu de validation est ensuite utilisé pour le calcul des métriques.
|**Contient moins&nbsp;de&nbsp;20 000&nbsp;lignes**| L’approche de validation croisée est appliquée. Le nombre de plis par défaut dépend du nombre de lignes. <br> **Si le jeu de données est inférieur à 1 000 lignes**, 10 plis sont utilisés. <br> **S’il y a entre 1 000 et 20 000 lignes**, trois plis sont utilisés.

## <a name="provide-validation-data"></a>Fournir des données de validation

Dans ce cas, vous pouvez démarrer avec un seul fichier de données et le fractionner en jeux de données de formation et de validation, ou vous pouvez fournir un fichier de données distinct pour le jeu de validation. Dans les deux cas, le paramètre `validation_data` de votre objet `AutoMLConfig` assigne les données à utiliser comme jeu de validation. Ce paramètre n’accepte que les jeux de données sous la forme d’un [jeu données Azure Machine Learning](how-to-create-register-datasets.md) ou d’un cadre de données Pandas.   

> [!NOTE]
> Le paramètre `validation_size` n’est pas pris en charge dans les scénarios de prévision.

L’exemple de code suivant définit explicitement la partie des données fournies dans `dataset` à utiliser pour l’apprentissage et la validation.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Fournir la taille du jeu de données de validation

Dans ce cas, un seul jeu de données est fourni pour l’expérience. Autrement dit, le paramètre `validation_data` n’est **pas** spécifié, et le jeu de données fourni est assigné au paramètre `training_data`.  

Dans votre objet `AutoMLConfig`, vous pouvez définir le paramètre `validation_size` pour qu’il contienne une partie des données d’apprentissage pour la validation. Cela signifie que le jeu de validation sera fractionné par Machine Learning automatisé à partir du paramètre initial `training_data` fourni. Cette valeur doit être comprise entre 0,0 et 1,0 non inclus (par exemple, 0,2 signifie que 20 % des données sont conservées pour les données de validation).

> [!NOTE]
> Le paramètre `validation_size` n’est pas pris en charge dans les scénarios de prévision. 

Prenez l'exemple du code suivant :

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>Validation croisée k-fold

Pour effectuer une validation croisée k-fold, incluez le paramètre `n_cross_validations` et affectez-lui une valeur. Ce paramètre définit le nombre de validations croisées à effectuer, en fonction du même nombre de plis.

> [!NOTE]
> Le paramètre `n_cross_validations` n’est pas pris en charge dans les scénarios de classification qui utilisent des réseaux neuronaux profonds.
 
Dans le code suivant, cinq plis pour la validation croisée sont définis. Par conséquent, il est question de cinq formations différentes. Chaque formation utilise 4/5 des données, et chaque validation utilise 1/5 des données avec un pli de données d'exclusion différent pour chaque formation.

Par conséquent, les mesures sont calculées avec la moyenne des cinq métriques de validation.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Validation croisée Monte-Carlo

Pour effectuer une validation croisée Monte-Carlo, incluez les paramètres `validation_size` et `n_cross_validations` dans votre objet `AutoMLConfig`. 

Pour la validation croisée de Monte-Carlo, Machine Learning automatisé met de côté la partie des données d’entraînement spécifiée par le paramètre `validation_size` pour la validation, puis affecte le reste des données pour l’entraînement. Ce processus est ensuite répété en fonction de la valeur précisée dans le paramètre `n_cross_validations`, ce qui génère de nouveaux fractionnements d’entraînement et de validation, de manière aléatoire, à chaque fois.

> [!NOTE]
> La validation croisée de Monte-Carlo n’est pas prise en charge dans les scénarios de prévision.

Le code suivant définit 7 replis pour la validation croisée tandis que 20 % des données d’entraînement seront utilisés pour la validation. Ainsi, il est question de 7 entraînements différents ; chaque entraînement utilise 80 % des données, et chaque validation utilise 20 % des données avec un repli de données d’exclusion différent à chaque fois.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Spécifier des plis de données de validation croisée personnalisés

Vous pouvez également fournir vos propres plis de données de validation croisée (CV). Cela est considéré comme un scénario plus avancé, car vous spécifiez les colonnes à fractionner et à utiliser pour la validation.  Incluez les colonnes de fractionnement CV personnalisées dans vos données d’apprentissage et spécifiez les colonnes en remplissant les noms de colonnes dans le paramètre `cv_split_column_names`. Chaque colonne représente un fractionnement de validation croisée et est remplie avec des valeurs entières 1 ou 0, où 1 indique que la ligne doit être utilisée pour la formation et 0 indique que la ligne doit être utilisée pour la validation.

L’extrait de code suivant contient des données de marketing bancaires avec deux colonnes de fractionnement CV « CV1 » et « CV2 ».

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Pour utiliser `cv_split_column_names` avec `training_data` et `label_column_name`, veuillez mettre à niveau votre Kit de développement logiciel (SDK) Python pour Azure Machine Learning version 1.6.0 ou ultérieure. Pour les versions précédentes du Kit de développement logiciel (SDK), consultez l’utilisation de `cv_splits_indices`, mais notez qu’il est utilisé avec les entrées de jeu de données `X` et `y` uniquement. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Calcul métrique pour la validation croisée dans le Machine Learning

Lorsque la validation croisée k-fold ou Monte Carlo est utilisée, les métriques sont calculées sur chaque pli de validation, puis agrégées. L’opération d’agrégation est une moyenne pour les métriques scalaires et une somme pour les graphiques. Les métriques calculées pendant la validation croisée sont basées sur tous les plis et, par conséquent, sur tous les échantillons du jeu de formation. [En savoir plus sur les métriques dans le Machine Learning automatisé](how-to-understand-automated-ml.md).

Lorsqu’un jeu de validation personnalisé ou un jeu de validation sélectionné automatiquement est utilisé, les métriques d’évaluation du modèle sont calculées uniquement à partir de ce jeu de validation, et non à partir des données de formation.

## <a name="next-steps"></a>Étapes suivantes

* [Empêcher les données déséquilibrées et le surajustement](concept-manage-ml-pitfalls.md).
* [Tutoriel : Utiliser le machine learning automatisé pour prédire le prix des courses de taxi : division des données](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)
* Guide pratique pour [entraîner automatiquement un modèle de prévision de série chronologique](how-to-auto-train-forecast.md).
