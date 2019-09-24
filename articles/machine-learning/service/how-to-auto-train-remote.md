---
title: Cibles de calcul distantes ML automatisées
titleSuffix: Azure Machine Learning
description: Découvrez comment créer des modèles avec le Machine Learning automatisé sur une cible de calcul distante Azure Machine Learning avec Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 9eab21fe6b5269229de186a7553e11a147c1033e
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034985"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Entraîner des modèles avec le machine learning automatisé dans le cloud

Dans Azure Machine Learning, vous effectuez l'apprentissage de votre modèle sur différents types de ressources de calcul que vous gérez. La cible de calcul peut être un ordinateur local ou une ressource dans le cloud.

Vous pouvez facilement faire monter en puissance/en charge votre expérience de Machine Learning. Pour cela, il vous suffit d’ajouter des cibles de calcul, par exemple Capacité de calcul Machine Learning (AmlCompute). AmlCompute est une infrastructure de capacité de calcul managée qui vous permet de créer facilement une capacité de calcul à un ou plusieurs nœuds.

Dans cet article, vous allez découvrir comment créer un modèle avec le Machine Learning automatisé avec AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>En quoi l’entraînement à distance diffère-t-il de l’entraînement local ?

Le tutoriel [Entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) vous explique comment utiliser un ordinateur local pour effectuer l’apprentissage d’un modèle avec le machine learning automatisé. Le workflow lors de l’entraînement local s’applique également aux cibles distantes. Cependant, avec le calcul distant, les itérations des expériences de machine learning automatisé sont exécutées de façon asynchrone. Cette fonctionnalité vous permet d’annuler une itération particulière, de suivre l’état de l’exécution et de continuer à travailler sur d’autres cellules dans le notebook Jupyter. Pour effectuer l’entraînement à distance, vous créez d’abord une cible de calcul distante, comme AmlCompute. Ensuite, vous configurez la ressource distante et vous y envoyez votre code.

Cet article décrit les étapes supplémentaires nécessaires pour exécuter une expérience de ML automatisé sur une cible AmlCompute distante. L’objet d’espace de travail, `ws`, du tutoriel est utilisé ici dans tout le code.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Créer une ressource

Créez la cible AmlCompute dans votre espace de travail (`ws`) si elle n’existe pas encore.

**Durée estimée** : La création de la cible AmlCompute prend environ 5 minutes.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Vous pouvez maintenant utiliser l’objet `compute_target` comme cible de calcul distante.

Voici les restrictions concernant le nom du cluster :
+ Il ne doit pas compter plus de 64 caractères.
+ Il ne doit pas inclure les caractères suivants : `\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-tabulardataset-function"></a>Accéder aux données à l’aide de la fonction TabularDataset

X et y définis en tant que `TabularDataset`, qui sont passés au Machine Learning automatisé dans AutoMLConfig. `from_delimited_files` par défaut définit `infer_column_types` sur True, ce qui déduit automatiquement le type des colonnes. 

Si vous souhaitez définir manuellement les types de colonnes, vous pouvez configurer l’argument `set_column_types` pour définir manuellement le type de chaque colonne. Dans l’exemple de code suivant, les données proviennent du package sklearn.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>Créer une configuration d’exécution

Pour que les dépendances soient disponibles pour le script get_data. py, définissez un objet `RunConfiguration`avec `CondaDependencies` défini. Utilisez cet objet pour le paramètre `run_configuration` dans `AutoMLConfig`.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

Consultez cet [exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) pour un autre exemple de ce modèle de conception.

## <a name="configure-experiment"></a>Configurer une expérience
Spécifiez les paramètres pour `AutoMLConfig`.  (Consultez la [liste complète des paramètres](how-to-configure-auto-train.md#configure-experiment) et leurs valeurs possibles.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>Activer les explications de modèle

Définissez le paramètre facultatif `model_explainability` dans le constructeur `AutoMLConfig`. En outre, un objet de dataframe de validation doit être passé en tant que paramètre `X_valid` pour utiliser la fonctionnalité d’explicabilité de modèle.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>Soumettre une expérience d’entraînement

À présent, soumettez la configuration afin de sélectionner automatiquement l’algorithme et les hyper-paramètres, et entraînez le modèle.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Le résultat ressemble à l’exemple suivant :

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Explorer les résultats

Vous pouvez utiliser le même [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) que celui du [tutoriel de formation](tutorial-auto-train-models.md#explore-the-results) pour afficher un graphique et le tableau des résultats.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Voici une image statique du widget.  Dans le bloc-notes, vous pouvez cliquer sur n’importe quelle ligne du tableau pour voir les propriétés d’exécution et les journaux d’activité de sortie pour cette série.   Vous pouvez également utiliser la liste déroulante au-dessus du graphe pour afficher un graphe de chaque métrique disponible pour chaque itération.

![tableau de widget](./media/how-to-auto-train-remote/table.png)
![tracé de widget](./media/how-to-auto-train-remote/plot.png)

Le widget affiche une URL qui vous permet de voir et d’explorer les différents détails de l’exécution.  

Si vous n’êtes pas dans un bloc-notes Jupyter, vous pouvez afficher l’URL à partir de l’exécution proprement dite :

```
remote_run.get_portal_url()
```

Les mêmes informations sont disponibles dans votre espace de travail.  Pour en savoir plus sur ces résultats, consultez [Comprendre les résultats du Machine Learning automatisé](how-to-understand-automated-ml.md).

### <a name="view-logs"></a>Afficher les journaux d’activité

Recherchez les journaux d’activité sur la DSVM sous `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="explain"></a> Meilleure explication de modèle

La récupération des données d’explication de modèle vous permet de voir des informations détaillées sur les modèles, de façon à rendre plus transparent ce qui se passe sur le back-end. Dans cet exemple, vous exécutez des explications de modèle uniquement pour le modèle le plus adapté. Si vous exécutiez cette opération pour tous les modèles dans le pipeline, cela entraînerait un temps d’exécution considérable. Les informations d’explication de modèle incluent les éléments suivants :

* shap_values : Les informations d’explication générées par la bibliothèque shap.
* expected_values : la valeur attendue du modèle appliqué au jeu de données X_train.
* overall_summary : Les valeurs d’importance des caractéristiques au niveau du modèle triées par ordre décroissant.
* overall_imp : Le nom des fonctionnalités triées dans le même ordre que dans overall_summary.
* per_class_summary : les valeurs d’importance des caractéristiques au niveau des classes triées par ordre décroissant. Disponible uniquement pour le cas de classification.
* per_class_imp : le nom des fonctionnalités triées dans le même ordre que dans per_class_summary. Disponible uniquement pour le cas de classification.

Le code suivant permet de sélectionner le meilleur pipeline à partir de vos itérations. La méthode `get_output` retourne la meilleure exécution ainsi que le modèle ajusté pour le dernier appel d’ajustement.

```python
best_run, fitted_model = remote_run.get_output()
```

Importez la fonction `retrieve_model_explanation` et exécutez-la sur le meilleur modèle.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Imprimez les résultats pour les variables d’explication `best_run` à afficher.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

L’impression des résultats du résumé d’explication `best_run` renvoie la sortie suivante.

![Sortie de la console d’explicabilité du modèle](./media/how-to-auto-train-remote/expl-print.png)

Vous pouvez également visualiser l’importance des caractéristiques dans l’interface utilisateur du widget, l’interface utilisateur web du Portail Azure ou la [page d’arrivée de votre espace de travail (préversion)](https://ml.azure.com). 

![Interface utilisateur d’explicabilité du modèle](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Exemples

Le notebook [how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) illustre les concepts présentés dans cet article.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Guide pratique pour configurer les paramètres d’entraînement automatique](how-to-configure-auto-train.md).
