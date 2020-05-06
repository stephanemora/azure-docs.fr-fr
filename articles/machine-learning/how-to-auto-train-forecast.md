---
title: Entraîner automatiquement un modèle de prévision de série chronologique
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser Azure Machine Learning pour entraîner un modèle de régression de prévisions de série chronologique en utilisant le Machine Learning automatisé.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 05d658c052c5bc12f49d957bb29ad085c269c57b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137356"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Entraîner automatiquement un modèle de prévision de série chronologique
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez apprendre à configurer et à entraîner un modèle de régression de prévisions de séries chronologiques à l’aide du Machine Learning dans Azure Machine Learning. 

La configuration d’un modèle de prévision est semblable à celle d’un modèle de régression standard utilisant le machine learning automatisé, mais il existe certaines options de configuration et étapes de prétraitement pour l’utilisation des données de série chronologique. 

Par exemple, vous pouvez [configurer](#config) l’horizon de prévision souhaité, les décalages et plus encore. Le Machine Learning automatisé effectue l’apprentissage d’un modèle unique, mais souvent ramifié en interne, pour tous les éléments du jeu de données et les horizons de prédiction. Plus de données sont ainsi disponibles pour estimer les paramètres du modèle et la généralisation en séries invisibles devient possible.

Les exemples suivants vous montrent comment :

* Préparer les données pour la modélisation de série chronologique
* Configurer les paramètres de série chronologique spécifiques dans un objet [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)
* Exécuter des prédictions avec les données de série chronologique

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Contrairement aux méthodes classiques de séries chronologiques, les valeurs des séries chronologiques passées du ML automatisé deviennent dynamiquement des dimensions supplémentaires pour le régresseur, avec d’autres prédicteurs. Cette approche intègre plusieurs variables contextuelles et les relations qu’elles entretiennent au cours de l’apprentissage. Dans la mesure où plusieurs facteurs peuvent influencer une prévision, cette méthode s’aligne bien sur les scénarios de prévision du monde réel. Par exemple, lors de la prévision des ventes, les interactions des tendances historiques, des taux de change et des prix déterminent conjointement le résultat de ventes. 

Les fonctionnalités extraites les données d’apprentissage jouent un rôle capital. Le ML automatisé effectue les étapes de prétraitement standard et génère des caractéristiques de séries chronologiques supplémentaires pour capturer les effets saisonniers et maximiser la précision des prévisions.

## <a name="time-series-and-deep-learning-models"></a>Modèles de série chronologique et de Deep Learning


Le Deep Learning du ML automatisé permet de prévoir des données de série chronologique univariées et multivariées.

Les modèles Deep Learning ont trois capacités intrinsèques :
1. Ils peuvent apprendre de mappages arbitraires des entrées aux sorties.
1. Ils prennent en charge plusieurs entrées et sorties.
1. Ils peuvent extraire automatiquement des modèles dans les données d’entrée qui s’étendent sur des séquences longues.

Avec des données plus volumineuses, les modèles de Deep Learning tels que ForecastTCN de Microsoft peuvent améliorer les scores du modèle qui en résulte. Découvrez comment [configurer votre expérience pour le Deep Learning](#configure-a-dnn-enable-forecasting-experiment).

Le ML automatisé fournit aux utilisateurs des modèles natifs de série chronologique et de Deep Learning dans le cadre du système de recommandation. 


Modèles| Description | Avantages
----|----|---
Prophet (préversion)|Prophet fonctionne mieux avec les séries chronologiques ayant des effets saisonniers forts et plusieurs saisons de données historiques. | Précis, rapide et robuste aux valeurs hors norme, aux données manquantes et aux évolutions profondes des séries chronologiques.
Auto-ARIMA (préversion)|La moyenne mobile intégrée de régression (ARIMA) est idéale pour les données fixes, c’est-à-dire les données dont les propriétés statistiques, comme la moyenne et la variance, sont constantes sur la totalité du jeu. Par exemple, si l’on lance une pièce de monnaie, la probabilité d’obtenir face est de 50 %, que ce soit aujourd’hui, demain ou l’année prochaine.| Idéal pour les séries univariées, car les valeurs passées sont utilisées pour prédire les valeurs futures.
ForecastTCN (préversion)| ForecastTCN est un modèle de réseau neuronal conçu pour traiter les tâches de prévision les plus exigeantes, capturant les tendances non linéaires locales et globales dans les données, ainsi que les relations entre les séries chronologiques.|Capable de mobiliser des tendances complexes dans les données et de s’adapter facilement aux jeux de données les plus volumineux.

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour créer l’espace de travail, voir [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
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

Dans ce cas, les données sont déjà triées dans l’ordre croissant par le champ d’heure `day_datetime`. Toutefois, lorsque vous configurez une expérience, vérifiez que la colonne d’heure souhaitée est triée dans l’ordre croissant pour générer une série chronologique valide. Supposons que les données contiennent 1 000 enregistrements et effectuez un fractionnement déterministe dans les données pour des jeux de données d’entraînement et de test. Identifiez le nom de la colonne d’étiquette et affectez-lui l’étiquette. Dans cet exemple, l’étiquette est `sales_quantity`. Séparez ensuite le champ d’étiquette de `test_data` pour former l’ensemble `test_target`.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Lors de l’entraînement d’un modèle pour la prévision de valeurs futures, assurez-vous que toutes les fonctionnalités utilisées pour l’entraînement peuvent être utilisées lors de l’exécution de prédictions à l’horizon prévu. Par exemple, lorsque vous créez une prévision de la demande, l’intégration d’une fonctionnalité pour le prix actuel du stock peut augmenter considérablement la précision de l’apprentissage. Toutefois, si vous prévoyez à un horizon lointain, vous ne pourrez peut-être pas prévoir avec précision les futures valeurs d’actions correspondant aux points de séries chronologiques futures et la précision du modèle pourrait en pâtir.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Données pour l’entraînement et la validation
Vous pouvez spécifier des jeux de données distincts pour l’entraînement et la validation directement dans le constructeur `AutoMLConfig`.

### <a name="rolling-origin-cross-validation"></a>Validation croisée à origine dynamique
La validation croisée à origine dynamique (ROCV) permet de fractionner les séries chronologiques de manière régulière dans le temps à des fins de prévisions. Elle divise la série en données d’apprentissage et données de validation à l’aide d’un point d’origine. Les échantillons de validation croisée sont générés par glissement de l’origine temporelle.  

![texte de remplacement](./media/how-to-auto-train-forecast/ROCV.svg)

Cette stratégie permet de préserver l’intégrité des données des séries chronologiques et d’éliminer le risque de fuite de données. La validation ROCV est utilisée automatiquement pour les tâches de prévision en transmettant conjointement les données d’apprentissage et de validation et en définissant le nombre d’échantillons de validation croisée à l’aide de `n_cross_validations`. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
En savoir plus sur [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Configurer et exécuter des expériences

Pour les tâches de prévision, le Machine Learning automatisé utilise des étapes de prétraitement et d’estimation qui sont spécifiques aux données de séries chronologiques. Les étapes de prétraitement suivantes seront exécutées :

* Détecter un exemple de fréquence de série chronologique (par exemple horaire, quotidien, hebdomadaire) et créer de nouveaux enregistrements pour les points temporels absents pour rendre la série continue.
* Imputer les valeurs manquantes dans la cible (via un préremplissage) et les colonnes de fonctionnalités (via les valeurs de colonne médiane)
* Créer des fonctionnalités granulaires pour permettre des effets fixes sur différentes séries
* Créer des caractéristiques temporelles pour faciliter l’apprentissage de modèles saisonniers
* Encoder des variables catégorielles avec des quantités numériques

L’objet [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) définit les paramètres et les données nécessaires pour une tâche de Machine Learning automatisé. Comme pour un problème de régression, vous définissez les paramètres d’entraînement standard comme type de tâche, le nombre d’itérations, les données d’apprentissage et le nombre de validations croisées. Pour les tâches de prévision, il existe des paramètres supplémentaires qui doivent être définis et qui affectent l’expérience. Le tableau suivant décrit chaque paramètre et son utilisation.

| Nom du&nbsp;paramètre | Description | Obligatoire |
|-------|-------|-------|
|`time_column_name`|Permet de spécifier la colonne DateHeure dans les données d’entrée utilisées pour la génération de la série chronologique et la déduction de sa fréquence.|✓|
|`grain_column_names`|Nom(s) définissant des groupes de séries individuelles dans les données d’entrée. Si le grain n’est pas défini, le jeu de données est considéré être une série chronologique.||
|`max_horizon`|Définit l’horizon maximal de prévision souhaité en unités de fréquence de série chronologique. Les unités sont basées sur l’intervalle de temps de vos données d’apprentissage (par exemple mensuelles ou hebdomadaires) que l’analyste doit prévoir.|✓|
|`target_lags`|Nombre de lignes selon lequel décaler les valeurs cibles en fonction de la fréquence des données. Le décalage est représenté sous la forme d’une liste ou d’un entier unique. Un décalage est nécessaire en l’absence de correspondance ou de corrélation par défaut des relations entre les variables indépendantes et la variable dépendante. Par exemple, quand vous essayez de prévoir la demande d’un produit, la demande mensuelle peut dépendre du prix de certaines matières premières trois mois auparavant. Dans ce cas, vous pouvez appliquer un décalage négatif de trois mois à la cible (la demande) afin que le modèle soit entraîné sur la relation appropriée.||
|`target_rolling_window_size`|*n* périodes historiques à utiliser pour générer des valeurs prédites, < = taille du jeu d’apprentissage. En cas d’omission, *n* est la taille du jeu d’apprentissage complet. Spécifiez ce paramètre si vous souhaitez prendre en compte seulement une partie des données historiques pour l’entraînement du modèle.||
|`enable_dnn`|Activez Prévision des DNN.||

Pour plus d’informations, consultez la [documentation de référence](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

Créez les paramètres de série chronologique en tant qu’objet de dictionnaire. Définissez l’élément `time_column_name` sur le champ `day_datetime` dans le jeu de données. Définissez le paramètre `grain_column_names` pour vous assurer que **deux groupes de séries chronologiques distincts** sont créés pour les données ; un pour le magasin A et B. Enfin, définissez l’élément `max_horizon` sur 50 afin de prédire le jeu de test complet. Définissez une fenêtre de prévision sur 10 périodes avec `target_rolling_window_size`, et spécifiez un décalage unique sur les valeurs cibles pour deux périodes futures avec le paramètre `target_lags`. Nous vous recommandons d’affecter la valeur « auto » à `max_horizon`, `target_rolling_window_size` et `target_lags` ; ces valeurs seront détectées automatiquement pour vous. Dans l'exemple ci-dessous, « auto » a été utilisé pour ces paramètres. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Les étapes de prétraitement du Machine Learning automatisé (normalisation des fonctionnalités, gestion des données manquantes, conversion de texte en valeurs numériques, etc.) font partie du modèle sous-jacent. Lorsque vous utilisez le modèle pour des prédictions, les étapes de prétraitement qui sont appliquées pendant l’entraînement sont appliquées automatiquement à vos données d’entrée.

En définissant le paramètre `grain_column_names` dans l’extrait de code ci-dessus, AutoML crée deux groupes de séries chronologiques distincts, également appelés séries chronologiques multiples. Si aucun grain n’est défini, AutoML suppose que le jeu de données est une série chronologique unique. Pour en savoir plus sur les séries chronologiques uniques, consultez [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Créez maintenant un objet `AutoMLConfig` standard, en spécifiant le type de tâche `forecasting` et soumettez l’expérience. Une fois le modèle terminé, récupérez la meilleure itération d’exécution.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Consultez les [exemples de notebooks de prévision](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) pour obtenir des exemples de code détaillés de la configuration de prévision avancée, notamment :

* [Détection et personnalisation de congé](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Validation croisée d’origine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Décalages configurables](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Caractéristiques des agrégations des périodes mobiles](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configurer une expérience de prévision d’activation des DNN

> [!NOTE]
> La prise en charge de DNN pour les prévisions dans le Machine Learning automatisé est en préversion et n’est pas prise en charge pour les exécutions locales.

Pour tirer parti des DNN pour les prévisions, vous devez définir le paramètre `enable_dnn` dans AutoMLConfig sur « true ». 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
En savoir plus sur [l’AutoMLConfig](#configure-and-run-experiment).

Vous pouvez également sélectionner l’option `Enable deep learning` dans le studio.
![texte de remplacement](./media/how-to-auto-train-forecast/enable_dnn.png)

Nous vous recommandons d’utiliser un cluster de calcul AML avec des références SKU GPU et au moins deux nœuds comme cible de calcul. Afin d’accorder suffisamment de temps pour que l’entraînement DNN se termine, nous vous recommandons de définir le délai d’expiration de l’expérience sur au moins quelques heures.
Pour plus d’informations sur le calcul AML et les tailles de machine virtuelle qui incluent des GPU, consultez la [documentation sur le calcul AML](how-to-set-up-training-targets.md#amlcompute) et la [documentation sur les tailles de machine virtuelle à GPU optimisé](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Pour obtenir un exemple de code détaillé utilisant des DNN, consultez [Beverage Production Forecasting notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

### <a name="target-rolling-window-aggregation"></a>Agrégation de fenêtres dynamiques cibles
La dernière valeur de la cible constitue souvent la meilleure information dont un prédicteur peut disposer. Le fait de créer des statistiques cumulatives de la cible est susceptible d’augmenter la précision des prédictions. Les agrégations de fenêtres dynamiques cibles permettent d’ajouter une agrégation dynamique de valeurs de données comme caractéristiques. Pour activer les fenêtres dynamiques cibles, définissez `target_rolling_window_size` sur la taille de fenêtre entière souhaitée. 

Prenons par exemple la prédiction de la demande d’énergie. Nous pouvons ajouter une caractéristique de fenêtre dynamique de trois jours pour tenir compte des modifications thermiques des espaces chauffés. Dans l’exemple ci-dessous, nous avons créé cette fenêtre de taille trois en définissant `target_rolling_window_size=3` dans le constructeur `AutoMLConfig`. Le tableau montre l’ingénierie de caractéristiques qui se produit lors de l’application de l’agrégation de fenêtres. Les colonnes valeur minimale, valeur maximale et somme sont générées sur une fenêtre glissante de trois en fonction des paramètres définis. Chaque ligne comporte une nouvelle caractéristique calculée. Dans le cas de l’horodatage du 8 septembre 2017 à 4 h, les valeurs maximale, minimale et somme sont calculées suivant les valeurs de la demande du 8 septembre 2017 entre 1 h et 3 h. Cette fenêtre de trois se déplace de façon à remplir les données des lignes restantes.

![texte de remplacement](./media/how-to-auto-train-forecast/target-roll.svg)

Le fait de générer ces nouvelles caractéristiques et de les utiliser comme données contextuelles supplémentaires contribue à la précision du modèle d’apprentissage.

Consultez un exemple de code Python tirant parti de la [caractéristique d’agrégation de fenêtres dynamiques cibles](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

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

La fonction `forecast()` doit être utilisée à la place de `predict()`, ce qui permet de spécifier le moment où les prédictions doivent démarrer. Dans l’exemple suivant, vous commencez par remplacer toutes les valeurs dans `y_pred` par `NaN`. L’origine de la prévision sera à la fin des données d’apprentissage dans ce cas, comme ce le serait normalement lors de l’utilisation de `predict()`. Toutefois, si vous avez remplacé uniquement le second semestre de `y_pred` par `NaN`, la fonction laisserait les valeurs numériques du premier semestre inchangées, mais prévoirait les valeurs `NaN` au second semestre. La fonction retourne à la fois les valeurs prédites et les fonctionnalités alignées.

Vous pouvez également utiliser le paramètre `forecast_destination` dans la fonction `forecast()` pour prévoir les valeurs jusqu’à une date spécifiée.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calculez la racine carrée de l’erreur quadratique moyenne (REQM) entre les valeurs `actual_labels` réelles et les valeurs prédites dans `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Maintenant que la précision du modèle global a été déterminée, l’étape suivante la plus réaliste consiste à utiliser le modèle pour prévoir des valeurs futures inconnues. Fournissez un jeu de données au même format que le jeu de test `test_data`, mais avec des dates/heures futures, et le leu de prédiction résultant correspond aux valeurs prédites pour chaque étape de la série chronologique. Supposons que les derniers enregistrements de la série chronologique dans le jeu de données aient été datés du 31/12/2018. Pour prévoir la demande pour le jour suivant (ou d’autant de périodes pour lesquelles vous avez besoin d’effectuer des prévisions, < = `max_horizon`), créez un seul enregistrement de série chronologique pour chaque magasin pour le 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Répétez les étapes nécessaires pour charger ces données futures dans une trame de données, puis exécutez `best_run.predict(test_data)` pour prédire les valeurs futures.

> [!NOTE]
> Les valeurs ne peuvent pas être prédites pour un nombre de périodes supérieur à `max_horizon`. Le modèle doit être ré-entraîné à un horizon plus lointain pour prédire les valeurs futures au-delà de l’horizon actuel.

## <a name="next-steps"></a>Étapes suivantes

* Suivez le [didacticiel](tutorial-auto-train-models.md) pour apprendre à créer des expériences avec le Machine Learning automatisé.
* Voir la documentation de référence sur le [Kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
