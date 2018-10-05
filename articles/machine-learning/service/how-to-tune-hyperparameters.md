---
title: Optimiser les hyperparamètres de votre modèle avec Azure Machine Learning
description: Découvrez comment optimiser les hyperparamètres de votre modèle d’apprentissage profond (deep learning) / machine learning avec le service Azure Machine Learning. Vous allez découvrir comment définir l’espace de recherche des paramètres, comment spécifier une métrique principale à optimiser et comment terminer de façon anticipée les configurations avec performances médiocres.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b6370fd9125c5b14df781b27e028c139175b7589
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405799"
---
# <a name="tune-hyperparameters-for-your-model"></a>Optimiser les hyperparamètres pour votre modèle

Dans cet article, vous découvrez comment optimiser efficacement les hyperparamètres pour votre modèle. Vous allez découvrir comment définir l’espace de recherche des paramètres, comment spécifier une métrique principale à optimiser et comment terminer de façon anticipée les configurations avec performances médiocres. Vous pouvez également visualiser les différentes exécutions d’entraînement et sélectionner la configuration la plus performante pour votre modèle.

## <a name="what-are-hyperparameters"></a>Présentation des hyperparamètres
Les hyperparamètres sont des paramètres ajustables choisis avant l’entraînement d’un modèle et qui régissent le processus d’entraînement lui-même. Par exemple, avant l’entraînement d’un réseau neuronal profond, vous devez décider du nombre de couches masquées dans le réseau et du nombre de nœuds dans chaque couche. Ces valeurs restent généralement constantes au cours du processus d’entraînement.

Dans les scénarios d’apprentissage profond / machine learning, les performances d’un modèle dépendent fortement des valeurs sélectionnées pour les hyperparamètres. L’objectif de l’exploration des hyperparamètres est de rechercher parmi les différentes configurations des hyperparamètres celle qui aboutit aux performances souhaitées. En règle générale, le processus d’exploration des hyperparamètres est manuel et minutieux, car l’espace de recherche est vaste et l’évaluation de chaque configuration peut être coûteuse en termes de ressources.

Azure Machine Learning vous permet d’automatiser cette exploration des hyperparamètres de façon efficace, ce qui vous permet de gagner du temps et d’économiser des ressources de façon significative. Vous pouvez spécifier la plage de valeurs des hyperparamètres à explorer, ainsi qu’un nombre maximal d’exécutions d’entraînement pour cette exploration. Le système lance alors automatiquement plusieurs exécutions simultanées d’entraînement avec différentes configurations des paramètres, puis recherche la configuration qui a obtenu les meilleures performances, mesurées selon une métrique choisie par l’utilisateur. Les exécutions d’entraînement ayant obtenu des performances médiocres sont arrêtées de façon anticipée, ce qui réduit considérablement le gaspillage des ressources de calcul. Ces ressources sont alors utilisées pour explorer d’autres configurations des hyperparamètres.

Pour pouvoir optimiser les hyperparamètres pour votre modèle avec le service Azure Machine Learning, vous devez effectuer les opérations suivantes :
* Définir l’espace de recherche des hyperparamètres
* Spécifier une métrique principale à optimiser
* Spécifier une stratégie d’arrêt anticipé
* Allouer des ressources pour l’optimisation des hyperparamètres
* Lancer une expérience avec la configuration ci-dessus

## <a name="define-the-hyperparameter-search-space"></a>Définir l’espace de recherche des hyperparamètres
Le service Azure Machine Learning optimise automatiquement les hyperparamètres en explorant la plage de valeurs définies pour chaque hyperparamètre.

### <a name="types-of-hyperparameters"></a>Types d’hyperparamètres
Chaque hyperparamètre peut être discret ou continu.

#### <a name="discrete-hyperparameters"></a>Hyperparamètres discrets 
Les hyperparamètres discrets peuvent être spécifiés en tant que `choice` entre des valeurs discrètes. `choice` peut prendre une ou plusieurs valeurs séparées par une virgule, un objet `range` ou n’importe quel objet `list` arbitraire. Par exemple :  

```Python
    {    
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Dans ce cas, batch_size peut prendre une des valeurs [16, 32, 64, 128] et number_of_hidden_layers peut prendre une des valeurs [1, 2, 3, 4].

Des hyperparamètres discrets avancés peuvent également être spécifiés en utilisant une distribution. Les distributions suivantes sont prises en charge :
* `quniform(low, high, q)` : retourne une valeur comme round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` : retourne une valeur comme round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` : retourne une valeur comme round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` : retourne une valeur comme round(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Hyperparamètres continus 
Les hyperparamètres continus peuvent être spécifiés sous forme de distribution sur une plage continue de valeurs. Les distributions prises en charge sont :
* `uniform(low, high)` : retourne une valeur uniformément distribuée entre low (valeur basse) et high (valeur haute)
* `loguniform(low, high)` : retourne une valeur calculée en fonction de exp(uniform(low, high)), de sorte que le logarithme de la valeur de retour est uniformément distribué
* `normal(mu, sigma)` : retourne une valeur réelle qui est normalement distribuée avec la moyenne mu et l’écart type sigma
* `lognormal(mu, sigma)` : retourne une valeur calculée en fonction de exp(normal(mu, sigma)), de sorte que le logarithme de la valeur de retour est normalement distribué

Voici un exemple de définition d’espace de paramètres :

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Cet exemple définit un espace de recherche avec deux paramètres : learning_rate et keep_probability. learning_rate aura une distribution normale avec une valeur moyenne de 10 et un écart type de 3. keep_probability aura une distribution uniforme avec une valeur minimale de 0,05 et une valeur maximale de 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Échantillonnage de l’espace des hyperparamètres
L’utilisateur spécifie également la méthode d’échantillonnage des paramètres à utiliser sur la définition d’espace des hyperparamètres spécifiée. Le service Azure Machine Learning prend en charge l’échantillonnage aléatoire, l’échantillonnage par grille et l’échantillonnage bayésien.

#### <a name="random-sampling"></a>Échantillonnage aléatoire
Dans l’échantillonnage aléatoire, les valeurs des hyperparamètres sont sélectionnées de façon aléatoire à partir de l’espace de recherche défini. L’échantillonnage aléatoire permet à l’espace de recherche d’inclure à la fois des hyperparamètres discrets et des hyperparamètres continus. Par exemple :

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Échantillonnage par grille
L’échantillonnage par grille effectue une simple recherche de grille sur toutes les valeurs possibles dans l’espace de recherche défini. Il peut être utilisé seulement avec des hyperparamètres spécifiés en utilisant `choice`. Par exemple, l’espace suivant a un total de six échantillons :

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Échantillonnage bayésien
L’échantillonnage bayésien est basé sur l’algorithme d’optimisation bayésienne et fait des choix intelligents quant aux valeurs des hyperparamètres pour l’échantillon suivant. Il sélectionne cet échantillon en fonction de la façon dont les échantillons précédents se sont comportés, de sorte que le nouvel échantillon améliore la métrique principale signalée.

Lors de l’utilisation d’un échantillonnage bayésien, le nombre d’exécutions simultanées a un impact sur l’efficacité du processus d’optimisation. En règle générale, un plus petit nombre d’exécutions simultanées peut aboutir à une meilleure convergence de l’échantillonnage. La raison en est que le degré plus petit de parallélisme augmente le nombre de d’exécutions qui bénéficient des exécutions effectuées précédemment.

L’échantillonnage bayésien prend en charge seulement les distributions `choice` et `uniform` sur l’espace de recherche. Par exemple : 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> L’échantillonnage bayésien ne prend actuellement pas en charge les stratégies d’arrêt anticipé (consultez [Spécifier une stratégie d’arrêt anticipé](#specify-an-early-termination-policy)). Si vous utilisez l’échantillonnage bayésien des paramètres, vous devez définir la stratégie sur `None`. Ne pas spécifier de stratégie d’arrêt avec l’échantillonnage bayésien aura le même effet.
>
> ```Python
> early_termination_policy = None
> ```

## <a name="specify-a-primary-metric-to-optimize"></a>Spécifier une métrique principale à optimiser
Lors de l’optimisation des hyperparamètres, vous devez spécifier la métrique principale que l’expérience d’optimisation des hyperparamètres doit optimiser. Chaque exécution d’entraînement est évaluée relativement à la métrique principale, et les exécutions avec des performances médiocres (celles où la métrique principale ne répond pas aux critères définis par la stratégie d’arrêt anticipé) sont arrêtées. En plus de spécifier le nom de la métrique principale, vous devez également spécifier l’objectif de l’optimisation, c’est-à-dire s’il faut maximiser ou minimiser la métrique principale.
* `primary_metric_name` : nom de la métrique principale à optimiser. Le nom de la métrique principale doit correspondre exactement au nom de la métrique journalisée par le script d’entraînement. Consultez [Journaliser des métriques pour l’optimisation des hyperparamètres](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal` : il peut s’agir de `PrimaryMetricGoal.MAXIMIZE` ou de `PrimaryMetricGoal.MINIMIZE`. Il détermine si la métrique principale est maximisée ou minimisée lors de l’évaluation des exécutions. 

Par exemple :
```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```
Ceci optimise les exécutions de façon à maximiser « accuracy » (précision).

### <a name="log-metrics-for-hyperparameter-tuning"></a>Journaliser des métriques pour l’optimisation des hyperparamètres
Pour pouvoir utiliser le service Azure Machine Learning pour l’optimisation des hyperparamètres, le script d’entraînement pour votre modèle doit signaler les métriques pertinentes pendant que le modèle s’exécute. L’utilisateur spécifie la métrique principale que le service doit utiliser pour évaluer les performances des exécutions, et le script d’entraînement doit journaliser cette métrique. Consultez [Spécifier une métrique principale à optimiser](#specify-a-primary-metric-to-optimize).

Vous pouvez mettre à jour votre script d’entraînement pour qu’il journalise cette métrique en utilisant l’extrait de code suivant :

```Python
from azureml.core.run import Run
run_logger = Run.get_submitted_run()
run_logger.log("accuracy", float(val_accuracy))
```

Dans cet exemple, le script d’entraînement calcule `val_accuracy` et journalise cette « précision », qui est utilisée comme métrique principale. Il incombe au développeur du modèle de déterminer la fréquence de collecte de cette métrique.

## <a name="specify-an-early-termination-policy"></a>Spécifier une stratégie d’arrêt anticipé
Lors de l’utilisation du service Azure Machine Learning pour optimiser les hyperparamètres, les exécutions avec des performances médiocres sont automatiquement arrêtées de façon anticipée. Ceci réduit considérablement le gaspillage des ressources, qui sont alors utilisées pour l’exploration d’autres configurations des paramètres.

Lors de l’utilisation d’une stratégie d’arrêt anticipé, un utilisateur peut configurer les paramètres suivants, qui contrôlent quand une stratégie est appliquée :
* `evaluation_interval` : fréquence d’application de la stratégie. Chaque journalisation de la métrique principale par le script d’entraînement compte pour un intervalle. Ainsi, une valeur de 1 pour `evaluation_interval` applique la stratégie chaque fois que le script d’entraînement signale la métrique principale. Une valeur de 2 pour `evaluation_interval` applique la stratégie à chaque autre signalement de la métrique principale par le script d’entraînement. Il s’agit d’un paramètre facultatif et, s’il n’est pas spécifié, `evaluation_interval` est défini par défaut sur 1.
* `delay_evaluation` : retarde la première évaluation de la stratégie pour un nombre d’intervalles spécifié. Il s’agit d’un paramètre facultatif qui permet à toutes les configurations de s’exécuter pour un nombre minimal initial d’intervalles, évitant un arrêt prématuré des exécutions d’entraînement. S’il est spécifié, la stratégie s’applique à chaque multiple de evaluation_interval qui est supérieur ou égal à delay_evaluation.

Le service Azure Machine Learning prend en charge les stratégies d’arrêt anticipé suivantes :

### <a name="bandit-policy"></a>Stratégie Bandit
La stratégie Bandit est une stratégie d’arrêt basée sur le facteur de marge/marge totale et l’intervalle d’évaluation. Cette stratégie arrête de façon anticipée les exécutions où la métrique principale n’est pas dans le facteur de marge/marge totale par rapport à l’exécution d’entraînement la plus performante. Elle prend les paramètres de configuration suivants :
* `slack_factor` ou `slack_amount` : marge autorisée par rapport à l’exécution d’entraînement la plus performante. `slack_factor` spécifie la marge autorisée sous forme de ratio. `slack_amount` spécifie la marge autorisée en valeur absolue, au lieu d’un ratio.

    Par exemple, considérez une stratégie Bandit appliquée avec un intervalle de 10. Supposez que l’exécution la plus performante à l’intervalle 10 a signalé une métrique principale de 0,8 avec l’objectif de maximiser la métrique principale. Si la stratégie a été spécifiée avec un `slack_factor` de 0,2, les exécutions d’entraînement, dont la meilleure mesure à l’intervalle 10 est inférieure à 0,66 (0,8/(1+`slack_factor`)) seront arrêtées. Si au lieu de cela, la stratégie a été spécifiée avec un `slack_amount` de 0,2, les exécutions d’entraînement, dont la meilleure mesure à l’intervalle 10 est inférieure à 0,6 (0,8 - `slack_amount`) seront arrêtées.
* `evaluation_interval` : fréquence d’application de la stratégie (paramètre facultatif).
* `delay_evaluation` : retarde la première évaluation de la stratégie pour un nombre d’intervalles spécifié (paramètre facultatif).

Considérez cet exemple :

```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Dans cet exemple, la stratégie d’arrêt anticipé est appliquée à chaque intervalle quand des métriques sont signalées, en commençant à l’intervalle d’évaluation 5. Toute exécution dont la meilleure métrique est inférieure à (1/(1+0,1) ou 91 % de la meilleure exécution sera arrêtée.

### <a name="median-stopping-policy"></a>Stratégie d’arrêt médiane
La stratégie d’arrêt médiane est une stratégie d’arrêt anticipé basée sur les moyennes mobiles des métriques principales signalées par les exécutions. Cette stratégie calcule les moyennes mobiles pour toutes les exécutions d’entraînement et arrête les exécutions dont les performances sont moins bonnes que la médiane des moyennes mobiles. Cette stratégie prend les paramètres de configuration suivants :
* `evaluation_interval` : fréquence d’application de la stratégie (paramètre facultatif).
* `delay_evaluation` : retarde la première évaluation de la stratégie pour un nombre d’intervalles spécifié (paramètre facultatif).

Considérez cet exemple :

```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Dans cet exemple, la stratégie d’arrêt anticipé est appliquée à chaque intervalle, en commençant à l’intervalle d’évaluation 5. Une exécution est interrompue à l’intervalle 5 si sa meilleure métrique principale est moins bonne que la médiane des moyennes mobiles sur les intervalles 1 à 5 pour toutes les exécutions d’entraînement.

### <a name="truncation-selection-policy"></a>Stratégie de sélection de troncation
La stratégie de sélection de troncation annule un pourcentage donné des exécutions les moins performantes à chaque intervalle d’évaluation. Les exécutions sont comparées en fonction de leur performance sur la métrique principale et les X % les moins bonnes sont arrêtées. Elle prend les paramètres de configuration suivants :
* `truncation_percentage` : le pourcentage des exécutions avec les performances les moins bonnes à arrêter à chaque intervalle d’évaluation. La valeur doit être un entier compris entre 1 et 99.
* `evaluation_interval` : fréquence d’application de la stratégie (paramètre facultatif).
* `delay_evaluation` : retarde la première évaluation de la stratégie pour un nombre d’intervalles spécifié (paramètre facultatif).

Considérez cet exemple :

```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Dans cet exemple, la stratégie d’arrêt anticipé est appliquée à chaque intervalle, en commençant à l’intervalle d’évaluation 5. Une exécution est arrêtée à l’intervalle 5 si sa performance à cet intervalle se trouve dans les 20 % des performances les moins bonnes de toutes les exécutions à l’intervalle 5.

### <a name="no-termination-policy"></a>Stratégie sans arrêt
Si vous voulez que toutes les exécutions d’entraînement s’exécutent jusqu’à leur terme, utilisez la stratégie sans arrêt. Son effet est qu’aucune stratégie d’arrêt anticipé n’est appliquée. Par exemple : 

```Python
from azureml.train.hyperdrive import NoTerminationPolicy
early_termination_policy = NoTerminationPolicy()
```

### <a name="default-policy"></a>Stratégie par défaut
Si aucune stratégie n’est spécifiée, le service d’optimisation des hyperparamètres utilise par défaut une stratégie d’arrêt médiane avec `evaluation_interval` égal à 1 et `delay_evaluation` égal à 5. Il s’agit de valeurs prudentes, qui peuvent fournir approximativement 25 à 35 % d’économies sans perte sur la métrique principale (d’après nos évaluations).

## <a name="allocate-resources-for-hyperparameter-tuning"></a>Allouer des ressources pour l’optimisation des hyperparamètres
Vous pouvez contrôler le budget nécessaire à vos ressources pour votre expérience d’optimisation des hyperparamètres en spécifiant le nombre total maximal d’exécutions d’entraînement et, si vous le souhaitez, la durée maximale de cette expérience (en minutes). 
* `max_total_runs` : nombre total maximal d’exécutions d’entraînement à créer. Il s’agit d’une limite supérieure : il peut y avoir moins d’exécutions, par exemple si l’espace des hyperparamètres est fini et a moins d’échantillons. Doit être un nombre compris entre 1 et 1000.
* `max_duration_minutes` : durée maximale en minutes de l’expérience d’optimisation des hyperparamètres. Il s’agit d’un paramètre facultatif et s’il est présent, les exécutions qui s’exécuteraient au-delà de cette limite de durée sont automatiquement annulées.

>[!NOTE] 
>Si `max_total_runs` et `max_duration_minutes` sont tous deux spécifiés, l’expérience d’optimisation des hyperparamètres est arrêtée quand le premier de ces deux seuils est atteint.

En outre, vous pouvez spécifier le nombre maximal d’exécutions d’entraînement qui peuvent s’exécuter simultanément pendant votre recherche d’optimisation des hyperparamètres.
* `max_concurrent_runs` : il s’agit du nombre maximal d’exécutions à exécuter simultanément à un moment donné. Si aucune valeur n’est spécifiée, toutes les `max_total_runs` exécutions sont lancées en parallèle. S’il est spécifié, sa valeur doit être un nombre compris entre 1 et 100.

>[!NOTE] 
>Le nombre d’exécutions simultanées est limité par les ressources disponibles dans la cible de calcul spécifiée. Vous devez donc vérifier que la cible de calcul dispose des ressources disponibles nécessaires pour les exécutions simultanées souhaitées.

Vous pouvez allouer des ressources pour l’optimisation des hyperparamètres comme indiqué dans cet exemple :
```Python
max_total_runs=20,
max_concurrent_runs=4
```
Ceci configure l’expérience d’optimisation des hyperparamètres pour utiliser un maximum de 20 exécutions au total, en exécutant 4 configurations à la fois.

## <a name="configure-your-hyperparameter-tuning-experiment"></a>Configurer votre expérience d’optimisation des hyperparamètres
Vous pouvez configurer votre expérience d’optimisation des hyperparamètres en utilisant l’espace de recherche des hyperparamètres défini, la stratégie d’arrêt anticipé, la métrique principale et l’allocation de ressources, qui sont décrits dans les sections ci-dessus. En outre, vous devez fournir un `estimator` qui sera appelé avec les hyperparamètres échantillonnés. Cet `estimator` décrit le script d’entraînement que vous exécutez, les ressources par travail (un seul GPU ou plusieurs) et la cible de calcul à utiliser. L’accès concurrentiel pour votre expérience d’optimisation des hyperparamètres étant limitée par les ressources disponibles, vous devez vérifier que votre cible de calcul spécifiée dans `estimator` a suffisamment de ressources pour l’accès concurrentiel souhaité. (Pour plus d’informations sur les estimateurs, consultez [Comment entraîner des modèles](how-to-train-ml-models.md)).

Voici un exemple de la façon dont vous pouvez configurer votre expérience d’optimisation des hyperparamètres :

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-your-hyperparameter-tuning-experiment"></a>Soumettre votre expérience d’optimisation des hyperparamètres
Une fois que vous avez défini votre configuration d’optimisation des hyperparamètres, vous pouvez soumettre une expérience avec cette configuration :

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

où `experiment_name` est le nom que vous voulez affecter à votre expérience d’optimisation des hyperparamètres, et `workspace` est l’espace de travail dans lequel vous voulez créer l’expérience (pour plus d’informations sur les expériences, suivez ce [lien](/concept-azure-machine-learning-architecture.md)).

## <a name="visualize-your-hyperparameter-tuning-experiment"></a>Visualiser votre expérience d’optimisation des hyperparamètres
Le SDK Azure Machine Learning fournit un widget de notebook qui peut être utilisé pour visualiser la progression de vos exécutions d’entraînement. L’extrait de code suivant peut être utilisé pour visualiser toutes vos exécutions d’optimisation des hyperparamètres au même endroit :

```Python
from azureml.train.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ceci affiche un tableau avec des détails sur les exécutions d’entraînement pour chacune des configurations d’hyperparamètres. Par exemple :

![Tableau des optimisations des hyperparamètres](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Vous pouvez également visualiser les performances de chacune des exécutions au fil de l’entraînement. Par exemple :

![Tracé de l’optimisation des hyperparamètres](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Enfin, vous pouvez identifier visuellement la corrélation entre les performances et les valeurs des hyperparamètres individuels avec un tracé de coordonnées parallèles. Par exemple : 

![Coordonnées parallèles de l’optimisation des hyperparamètres](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Vous pouvez aussi visualiser toutes vos exécutions d’optimisation des hyperparamètres dans le portail web Azure. Pour plus d’informations sur la visualisation d’une expérience dans le portail web, suivez ce [lien](/how-to-track-experiments.md/#view-the-experiment-in-the-web-portal). Par exemple :

![Portail de l’optimisation des hyperparamètres](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-configuration-that-resulted-in-the-best-performance"></a>Rechercher la configuration qui a obtenu les meilleures performances
Une fois que toutes les exécutions d’optimisation des hyperparamètres sont terminées, vous pouvez identifier la configuration la plus performante et les valeurs des hyperparamètres correspondants en utilisant l’extrait de code suivant :

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Exemple de notebook
Reportez-vous à 
* `training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb` pour obtenir un tutoriel sur l’optimisation des hyperparamètres pour un modèle Tensorflow. 

Consultez ce bloc-notes :

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Faire le suivi d’une expérience](how-to-track-experiments.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
