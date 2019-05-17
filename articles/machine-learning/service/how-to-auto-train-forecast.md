---
title: Automatique-former un modèle de prévision de séries chronologiques
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le service Azure Machine Learning pour l’apprentissage d’une série chronologique prévision de modèle de régression avec automatisée l’apprentissage.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: c7f4b6d8aa614a460772fb7af11f9b83dc3fc979
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800809"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatique-former un modèle de prévision de séries chronologiques

Dans cet article, vous allez apprendre à former un modèle de régression prévision de séries chronologiques à l’aide d’apprentissage automatique dans le service Azure Machine Learning. La configuration d’un modèle de prévision est semblable à la configuration d’un modèle de régression standard à l’aide d’apprentissage automatique, mais certaines étapes de prétraitement et les options de configuration existent pour l’utilisation des données de série chronologique. Les exemples suivants montrent comment à :

* Préparer les données pour la modélisation de série chronologique
* Configurer les paramètres de série chronologique spécifiques dans un [ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objet
* Exécutez des prédictions avec les données de séries chronologiques

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

## <a name="prerequisites"></a>Conditions préalables

* Un espace de travail de service Microsoft Azure Machine Learning. Pour créer l’espace de travail, consultez [créer un espace de travail du service Azure Machine Learning](setup-create-workspace.md).
* Cet article suppose une connaissance de base sur la configuration d’une expérience d’apprentissage automatique. Suivez le [didacticiel](tutorial-auto-train-models.md) ou [procédures](how-to-configure-auto-train.md) pour afficher la base automatisés d’apprentissage des modèles de conception expérience.

## <a name="preparing-data"></a>Préparation des données

La principale différence entre un type de tâche de régression et de régression prévision type de tâche dans l’apprentissage automatique est notamment une fonctionnalité dans vos données qui représente une série d’heure valide. Une série chronologique standard a une fréquence bien définie et cohérente et a une valeur à chaque point de l’exemple dans un intervalle de temps continue. Envisagez l’instantané suivant d’un fichier `sample.csv`.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Ce jeu de données est un exemple simple de données de ventes quotidiennes d’une société qui a deux magasins différents, A et B. en outre, il existe une fonctionnalité pour `week_of_year` qui permettra le modèle détecter la saisonnalité hebdomadaire. Le champ `day_datetime` représente une série chronologique propre avec une fréquence quotidienne et le champ `sales_quantity` est la colonne cible pour l’exécution de prédictions. Lire les données dans une trame de données Pandas, puis utilisez le `to_datetime` (fonction) pour vous assurer de la série chronologique est un `datetime` type.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Dans ce cas les données sont déjà triées par ordre croissant par le champ d’heure `day_datetime`. Toutefois, lorsque vous configurez une expérience, vérifiez que la colonne de temps souhaitée est triée dans l’ordre croissant, pour générer une série chronologique valide. Supposons que les données contiennent 1 000 enregistrements et effectuez un fractionnement déterministe dans les données à créer d’apprentissage et jeux de données de test. Puis séparer le champ cible `sales_quantity` pour créer la formation de prédiction et de test définit.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Lors de l’apprentissage d’un modèle pour la prévision de valeurs futures, assurez-vous que toutes les fonctionnalités utilisées pour l’apprentissage peuvent être utilisées lors de l’exécution de prédictions pour votre horizon prévue. Par exemple, lorsque vous créez une prévision de la demande, y compris une fonctionnalité pour le prix actuel du stock peut augmenter massivement précision de l’apprentissage. Toutefois, si vous prévoyez un long horizon de prévision, vous n’êtes peut-être pas en mesure de prévoir avec précision les futures valeurs d’actions correspondant aux points de séries chronologiques futures et précision du modèle peut en ressentir.

## <a name="configure-and-run-experiment"></a>Configurer et exécuter l’expérience

Pour la prévision des tâches, apprentissage automatisé utilise des étapes de prétraitement et de la fonctionnalité d’estimation qui sont spécifiques aux données de séries chronologiques. Les étapes de prétraitement suivantes sont exécutées :

* Détecter les séries chronologiques exemple fréquence (par exemple, horaire, quotidienne, hebdomadaire) et créer de nouveaux enregistrements d’absence de points dans le temps pour rendre la série continue.
* Imputer les valeurs manquantes dans la cible (par le biais de remplissage avant) et les colonnes de fonctionnalités (à l’aide de valeurs de colonne médiane)
* Créer en fonction du niveau de granularité des fonctionnalités pour activer les effets fixes dans différentes séries
* Créer des caractéristiques temporelles pour simplifier l’apprentissage des schémas saisonniers
* Encoder des variables catégorielles de quantités numériques

Le `AutoMLConfig` objet définit les paramètres et les données nécessaires pour une tâche d’apprentissage machine automatisée. Comme pour un problème de régression, vous définissez les paramètres de formation standard comme type de tâche, nombre d’itérations, les données d’apprentissage et nombre de validations de croisée. Tâches de prévision, il existe de paramètres supplémentaires qui doivent être définies et qui affectent l’expérience. Le tableau suivant décrit chaque paramètre et son utilisation.

| Paramètre | Description  | Obligatoire |
|-------|-------|-------|
|`time_column_name`|Permet de spécifier la colonne datetime dans les données d’entrée utilisées pour la création de la série chronologique et de déduction sa fréquence.|✓|
|`grain_column_names`|Noms de définition de groupes de séries individuelles dans les données d’entrée. Si le fragment n’est pas défini, le jeu de données est supposé être une série chronologique.||
|`max_horizon`|Nombre maximal souhaité horizon de prévision en unités de fréquence de série chronologique.|✓|
|`target_lags`|*n* périodes de retard de transfert des valeurs avant l’apprentissage du modèle ciblent.||
|`target_rolling_window_size`|*n* périodes historiques à utiliser pour générer des valeurs prédites, < = taille du jeu de formation. Si omis, *n* est taille de jeu d’apprentissage complet.||

Créer les paramètres de série chronologique en tant qu’un objet de dictionnaire. Définir le `time_column_name` à la `day_datetime` champ dans le jeu de données. Définir le `grain_column_names` paramètre pour vous assurer que **deux séparent les groupes de séries chronologiques** créés pour les données ; un pour le magasin A et B. Enfin, définissez le `max_horizon` 50 afin de prédire le test complet à définir. Définir une fenêtre de prévision à 10 périodes avec `target_rolling_window_size`et la cible 2 périodes à l’avance avec les valeurs de décalage le `target_lags` paramètre.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Créez maintenant une norme `AutoMLConfig` de l’objet, en spécifiant le `forecasting` type de tâche et soumettre l’expérimentation. Une fois le modèle terminé, récupérez l’itération exécution mieux.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> Pour la procédure (CV) de la validation croisée, les données de série chronologique peuvent violer les hypothèses statistiques de base de la stratégie de validation croisée de K-pli canonique, afin de l’apprentissage automatisé implémente une procédure de validation d’origine propagée à créer plis de validation croisée pour les données de série chronologique. Pour utiliser cette procédure, vous devez spécifier le `n_cross_validations` paramètre dans le `AutoMLConfig` objet. Vous pouvez ignorer la validation et l’utilisation de votre propre validation définit avec la `X_valid` et `y_valid` paramètres.

### <a name="view-feature-engineering-summary"></a>Afficher le résumé ingénierie fonctionnalité

Pour les types de tâche de séries chronologiques dans l’apprentissage automatique, vous pouvez afficher les détails de la fonctionnalité de processus de conception. Le code suivant montre chaque fonctionnalité brutes, ainsi que les attributs suivants :

* Nom de la fonctionnalité brutes
* Nombre de caractéristiques conçues formé en dehors de cette fonctionnalité brutes
* Type détecté
* Indique si la fonctionnalité a été supprimée
* Liste des transformations de fonctionnalité pour la fonctionnalité brutes

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Meilleur modèle de prévision

Utiliser l’itération de modèle meilleures pour prévoir les valeurs du jeu de données de test.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Vous pouvez également utiliser le `forecast()` fonction au lieu de `predict()`, ce qui permettra de spécifications de lorsque les prédictions doivent démarrer. Dans l’exemple suivant, vous remplacez tout d’abord toutes les valeurs de `y_pred` avec `NaN`. L’origine de la prévision sera à la fin des données d’apprentissage dans ce cas, comme il le serait normalement lors de l’utilisation `predict()`. Toutefois, si vous avez remplacé uniquement le second semestre `y_pred` avec `NaN`, la fonction laisserait les valeurs numériques dans la première moitié non modifié, mais la prévision le `NaN` valeurs dans la deuxième moitié. La fonction retourne les valeurs prédites et les fonctionnalités alignées.

Vous pouvez également utiliser le `forecast_destination` paramètre dans le `forecast()` fonction de prévision des valeurs jusqu'à une date spécifiée.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calculer RMSE (l’erreur quadratique moyenne racine) entre le `y_test` valeurs réelles et les valeurs prédites dans `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Maintenant que l’ensemble précision du modèle a été déterminée, l’étape suivante plus réaliste consiste à utiliser le modèle pour prévoir les futures valeurs inconnues. Il suffit de fournir un jeu de données dans le même format que le jeu de test `X_test` mais avec des dates/heures futures et la prédiction résultant ensemble est les valeurs prédites pour chaque étape de la série chronologique. Supposons que les derniers enregistrements de série chronologique dans le jeu de données ont été pour le 31/12/2018. Pour prévoir la demande pour le jour suivant (ou des périodes autant que nécessaire pour effectuer des prévisions, < = `max_horizon`), créez un seul enregistrement de la série de temps pour chaque magasin pour le 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Répétez les étapes nécessaires pour charger ces données futures à une trame de données, puis exécutez `best_run.predict(X_test)` pour prédire des valeurs futures.

> [!NOTE]
> Valeurs ne sont pas prévisible pour le nombre de périodes supérieure à la `max_horizon`. Le modèle doit être formé de nouveau avec un plus grande horizon pour prévoir les valeurs au-delà de l’horizon actuel.

## <a name="next-steps"></a>Étapes suivantes

* Suivez le [didacticiel](tutorial-auto-train-models.md) pour apprendre à créer des expériences avec automatisée apprentissage.
* Afficher le [Azure Machine Learning SDK pour Python](https://aka.ms/aml-sdk) documentation de référence.
