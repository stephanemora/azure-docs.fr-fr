---
title: Entraîner automatiquement un modèle de prévision de série chronologique
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser Azure Machine Learning service pour entraîner un modèle de régression de prévisions de série chronologique en utilisant le machine learning automatisé.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 34902aa23339b62920f918ae19b410a99e226a0e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358797"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Entraîner automatiquement un modèle de prévision de série chronologique

Dans cet article, vous allez apprendre à former un modèle de régression de prévisions de série chronologique à l’aide du machine learning dans Azure Machine Learning service. La configuration d’un modèle de prévision est semblable à celle d’un modèle de régression standard utilisant le machine learning automatisé, mais il existe certaines options de configuration et étapes de prétraitement pour l’utilisation des données de série chronologique. Les exemples suivants vous montrent comment :

* Préparer les données pour la modélisation de série chronologique
* Configurer les paramètres de série chronologique spécifiques dans un objet [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)
* Exécuter des prédictions avec les données de série chronologique

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Vous pouvez utiliser ML automatisé pour combiner des techniques et des approches afin d’obtenir des prévisions de séries chronologiques recommandées et de qualité. Une expérience de série chronologique automatisée est traitée comme un problème de régression multivariable. Les valeurs de série chronologique passées « pivotent » pour devenir des dimensions supplémentaires pour le régresseur, avec d’autres prédicteurs. 

Contrairement aux méthodes de séries chronologiques classique, cette méthode présente l’avantage d’incorporer naturellement plusieurs variables contextuelles et leurs relations entre elles pendant l’apprentissage. Dans les applications de prévisions du monde réel, plusieurs facteurs peuvent influencer une prévision. Par exemple, lors de la prévision des ventes, les interactions des tendances historiques, des taux de change et des prix déterminent conjointement le résultat de ventes. Un autre avantage est que toutes les innovations récentes dans les modèles de régression appliquent immédiatement aux prévisions.

Vous pouvez [configurer](#config) l’horizon souhaité pour la prévision, ainsi que des retards et bien plus encore. ML automatisé entraîne un modèle unique, mais comportant souvent des branches en interne pour tous les éléments du jeu de données et des horizons de prédiction. Plus de données sont donc disponibles pour estimer les paramètres du modèle, et il devient possible de généraliser des séries inconnues. 

Les fonctionnalités extraites les données d’apprentissage jouent un rôle capital. ML automatisé effectue les étapes de prétraitement standard et génère des fonctionnalités supplémentaires de séries chronologiques pour capturer les effets saisonniers et optimiser la précision des prévisions. 

## <a name="prerequisites"></a>Prérequis

* Un espace de travail de service Microsoft Azure Machine Learning. Pour créer l’espace de travail, consultez [Créer un espace de travail Azure Machine Learning service](setup-create-workspace.md).
* Cet article suppose une connaissance de base en matière de configuration d’une expérience de Machine Learning automatisé. Suivez le [didacticiel](tutorial-auto-train-models.md) ou les [procédures](how-to-configure-auto-train.md) pour afficher les modèles de conception des expériences de Machine Learning automatisé.

## <a name="preparing-data"></a>Préparation des données

La principale différence entre un type de tâche de régression de prévisions et un type de tâche de régression au sein d’un système de Machine Learning automatisé consiste à intégrer une fonctionnalité dans vos données qui représente une série chronologique valide. Une série chronologique normale a une fréquence cohérente et bien définie et a une valeur à chaque point de l’exemple dans un intervalle de temps continu. Examinons la capture instantanée suivante d’un fichier `sample.csv`.

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

Ce jeu de données est un exemple simple de données de ventes quotidiennes d’une société qui possède deux magasins différents, A et B. en outre, il existe une fonctionnalité pour `week_of_year` qui permettra au modèle de détecter la saisonnalité hebdomadaire. Le champ `day_datetime` représente une série chronologique propre avec une fréquence quotidienne et le champ `sales_quantity` est la colonne cible pour l’exécution de prédictions. Lisez les données dans une trame de données Pandas, puis utilisez la fonction `to_datetime` pour vous assurer que la série chronologique est un type `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Dans ce cas, les données sont déjà triées dans l’ordre croissant par le champ d’heure `day_datetime`. Toutefois, lorsque vous configurez une expérience, vérifiez que la colonne d’heure souhaitée est triée dans l’ordre croissant pour générer une série chronologique valide. Supposons que les données contiennent 1 000 enregistrements et effectuez un fractionnement déterministe dans les données pour des jeux de données d’entraînement et de test. Séparez ensuite le champ cible `sales_quantity` pour créer l’entraînement de prédiction et tester les jeux.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Lors de l’entraînement d’un modèle pour la prévision de valeurs futures, assurez-vous que toutes les fonctionnalités utilisées pour l’entraînement peuvent être utilisées lors de l’exécution de prédictions à l’horizon prévu. Par exemple, lorsque vous créez une prévision de la demande, l’intégration d’une fonctionnalité pour le prix actuel du stock peut augmenter considérablement la précision de l’apprentissage. Toutefois, si vous prévoyez à un horizon lointain, vous ne pourrez peut-être pas prévoir avec précision les futures valeurs d’actions correspondant aux points de séries chronologiques futures et la précision du modèle pourrait en pâtir.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Configurer et exécuter des expériences

Pour les tâches de prévision, le Machine Learning automatisé utilise des étapes de prétraitement et d’estimation qui sont spécifiques aux données de séries chronologiques. Les étapes de prétraitement suivantes seront exécutées :

* Détecter un exemple de fréquence de série chronologique (par exemple, horaire, quotidien, hebdomadaire) et créer de nouveaux enregistrements pour les points temporels absents pour faire une série continue.
* Imputer les valeurs manquantes dans la cible (via un préremplissage) et les colonnes de fonctionnalités (via les valeurs de colonne médiane)
* Créer des fonctionnalités granulaires pour permettre des effets fixes sur différentes séries
* Créer des caractéristiques temporelles pour faciliter l’apprentissage de modèles saisonniers
* Encoder des variables catégorielles avec des quantités numériques

L’objet `AutoMLConfig` définit les paramètres et les données nécessaires pour une tâche de Machine Learning automatisé. Comme pour un problème de régression, vous définissez les paramètres d’entraînement standard comme type de tâche, le nombre d’itérations, les données d’apprentissage et le nombre de validations croisées. Pour les tâches de prévision, il existe des paramètres supplémentaires qui doivent être définis et qui affectent l’expérience. Le tableau suivant décrit chaque paramètre et son utilisation.

| Paramètre | Description | Obligatoire |
|-------|-------|-------|
|`time_column_name`|Permet de spécifier la colonne DateHeure dans les données d’entrée utilisées pour la génération de la série chronologique et la déduction de sa fréquence.|✓|
|`grain_column_names`|Nom(s) définissant des groupes de séries individuelles dans les données d’entrée. Si le grain n’est pas défini, le jeu de données est considéré être une série chronologique.||
|`max_horizon`|Définit l’horizon maximal de prévision souhaité en unités de fréquence de série chronologique. Les unités sont basées sur l’intervalle de temps de vos données d’apprentissage (par exemple, mensuelles, hebdomadaires) que l’analyste doit prévoir.|✓|
|`target_lags`|*n* périodes pour décaler vers l’avant les valeurs cibles avant l’entraînement du modèle.||
|`target_rolling_window_size`|*n* périodes historiques à utiliser pour générer des valeurs prédites, < = taille du jeu d’apprentissage. En cas d’omission, *n* est la taille du jeu d’apprentissage complet.||

Créez les paramètres de série chronologique en tant qu’objet de dictionnaire. Définissez l’élément `time_column_name` sur le champ `day_datetime` dans le jeu de données. Définissez le paramètre `grain_column_names` pour vous assurer que **deux groupes de séries chronologiques distincts** sont créés pour les données ; un pour le magasin A et B. Enfin, définissez l’élément `max_horizon` sur 50 afin de prédire le jeu de test complet. Définissez une fenêtre de prévision sur 10 périodes avec `target_rolling_window_size`, et décalez les valeurs cibles de 2 périodes à l’avance avec le paramètre `target_lags`.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Créez maintenant un objet `AutoMLConfig` standard, en spécifiant le type de tâche `forecasting` et soumettez l’expérience. Une fois le modèle terminé, récupérez la meilleure itération d’exécution.

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
> Pour la procédure de validation croisée, les données de série chronologique peuvent enfreindre les hypothèses statistiques de base de la stratégie de validation croisée K-Fold canonique, de façon à ce que le Machine Learning automatisé implémente une procédure de validation propagée pour créer des plis de validation croisée pour les données de série chronologique. Pour utiliser cette procédure, spécifiez le paramètre `n_cross_validations` dans l’objet `AutoMLConfig`. Vous pouvez ignorer la validation et utiliser vos propres jeux de validation avec les paramètres `X_valid` et `y_valid`.

### <a name="view-feature-engineering-summary"></a>Afficher le résumé de l’ingénierie des caractéristiques

Pour les types de tâche de séries chronologiques dans le Machine Learning automatisé, vous pouvez afficher les détails à partir du processus d’ingénierie des fonctionnalités. Le code suivant montre chaque fonctionnalité brute, ainsi que les attributs suivants :

* Nom de la fonctionnalité brute
* Nombre de fonctionnalités conçues formées à partir de cette fonctionnalité brute
* Type détecté
* Abandon ou non de la fonctionnalité
* Liste des transformations de fonctionnalités pour la fonctionnalité brute

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prévision avec le meilleur modèle

Utilisez la meilleure itération de modèle pour prévoir les valeurs du jeu de données de test.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Vous pouvez également utiliser la fonction `forecast()` au lieu de `predict()`, ce qui permet des spécifications précisant quand les prédictions doivent démarrer. Dans l’exemple suivant, vous commencez par remplacer toutes les valeurs dans `y_pred` par `NaN`. L’origine de la prévision sera à la fin des données d’apprentissage dans ce cas, comme ce le serait normalement lors de l’utilisation de `predict()`. Toutefois, si vous avez remplacé uniquement le second semestre de `y_pred` par `NaN`, la fonction laisserait les valeurs numériques du premier semestre inchangées, mais prévoirait les valeurs `NaN` au second semestre. La fonction retourne à la fois les valeurs prédites et les fonctionnalités alignées.

Vous pouvez également utiliser le paramètre `forecast_destination` dans la fonction `forecast()` pour prévoir les valeurs jusqu’à une date spécifiée.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calculez la racine carrée de l’erreur quadratique moyenne (REQM) entre les valeurs `y_test` réelles et les valeurs prédites dans `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Maintenant que la précision du modèle global a été déterminée, l’étape suivante la plus réaliste consiste à utiliser le modèle pour prévoir des valeurs futures inconnues. Il suffit de fournir un jeu de données dans le même format que le jeu de test `X_test`, mais avec des dates/heures futures et le leu de prédiction résultant correspond aux valeurs prédites pour chaque étape de la série chronologique. Supposons que les derniers enregistrements de la série chronologique dans le jeu de données aient été datés du 31/12/2018. Pour prévoir la demande pour le jour suivant (ou d’autant de périodes pour lesquelles vous avez besoin d’effectuer des prévisions, < = `max_horizon`), créez un seul enregistrement de série chronologique pour chaque magasin pour le 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Répétez les étapes nécessaires pour charger ces données futures dans une trame de données, puis exécutez `best_run.predict(X_test)` pour prédire les valeurs futures.

> [!NOTE]
> Les valeurs ne peuvent pas être prédites pour un nombre de périodes supérieur à `max_horizon`. Le modèle doit être ré-entraîné à un horizon plus lointain pour prédire les valeurs futures au-delà de l’horizon actuel.

## <a name="next-steps"></a>Étapes suivantes

* Suivez le [didacticiel](tutorial-auto-train-models.md) pour apprendre à créer des expériences avec le Machine Learning automatisé.
* Voir la documentation de référence sur le [Kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk).
