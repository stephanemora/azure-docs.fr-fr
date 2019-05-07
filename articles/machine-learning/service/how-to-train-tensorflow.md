---
title: Former des modèles avec TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Découvrez comment exécuter à nœud unique et la formation distribuée des modèles TensorFlow et Keras avec TensorFlow et Keras estimateurs
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: c8865c851f394d73b5446ac159b5a7799c0c9ed2
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192346"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Former des modèles TensorFlow et Keras avec le service Azure Machine Learning

Vous pouvez facilement exécuter des travaux de formation TensorFlow sur Azure compute à l’aide de la [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) classe estimateur dans le Kit de développement logiciel Azure Machine Learning. Le `TensorFlow` estimateur dirige le service Azure Machine Learning pour exécuter votre tâche sur un conteneur compatible avec TensorFlow pour l’apprentissage du réseau neuronal profond (DNN).

Le `TensorFlow` estimateur fournit également une couche d’abstraction sur l’exécution, ce qui signifie que vous pouvez facilement configurer paramétrable s’exécute sur différentes cibles de calcul sans modifier vos scripts de formation.

## <a name="getting-started"></a>Prise en main

Envoi de travaux avec le `TensorFlow` estimateur est similaire à l’aide de la base de [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py). Par conséquent, nous recommandons de commencer par lire le [base article de procédure estimateur](how-to-train-ml-models.md) pour d’abord comprendre les concepts principale.

Si vous voulez vous familiariser avec le service Azure Machine Learning, [terminer le démarrage rapide](quickstart-run-cloud-notebook.md). Vous avez un environnement de travail chargé avec tous nos [exemples de blocs-notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml).

## <a name="single-node-training"></a>Entraînement à nœud unique

Pour exécuter une tâche TensorFlow, instanciez un [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) de l’objet et les transmettent sous une expérience.

Le code suivant instancie un estimateur TensorFlow et la soumet sous une expérience. Le script de formation `train.py` exécutera en utilisant les paramètres de script donné. Le travail est exécuté sur un processeur graphique prenant en charge [cible de calcul](how-to-set-up-training-targets.md)et scikit-Découvrez va être installé en tant que dépendance pour `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Entraînement distribué

Le [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimateur prend également en charge la formation distribuée entre les clusters GPU et UC. Vous pouvez facilement exécuter des tâches TensorFlow distribuées et le service Azure Machine Learning géreront l’infrastructure et l’orchestration pour vous.

Service Azure Machine Learning prend en charge deux méthodes de formation distribuée TensorFlow :

* [En fonction de MPI](https://www.open-mpi.org/) distribué à l’aide de la formation la [Horovod](https://github.com/uber/horovod) framework
* Natif [distributed TensorFlow](https://www.tensorflow.org/deploy/distributed) à l’aide de la méthode de serveur de paramètre

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) est une infrastructure open source pour la formation distribuée développé par Uber. Il offre un accès facile aux travaux de GPU TensorFlow distribuées.

L’exemple suivant exécute une tâche de la formation distribuée à l’aide de Horovod avec deux workers sont répartis entre deux nœuds.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod et ses dépendances doit être installés pour vous, afin de pouvoir l’importer dans votre script de formation.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Serveur de paramètres

Vous pouvez également exécuter l’entraînement [natif distribué TensorFlow](https://www.tensorflow.org/deploy/distributed), qui utilise le modèle de serveur de paramètres. Dans cette méthode, vous effectuez un entraînement sur un cluster de serveurs de paramètres et de workers. Les workers calculent les dégradés pendant l’entraînement, alors que les serveurs de paramètres agrègent les dégradés.

L’exemple suivant exécute une tâche de la formation distribuée à l’aide de la méthode du paramètre serveur avec quatre workers réparties entre deux nœuds.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Remarque sur `TF_CONFIG`

Vous devez également les adresses réseau et les ports du cluster pour le [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), alors Azure Machine Learning définit la `TF_CONFIG` variable d’environnement pour vous.

La variable d’environnement `TF_CONFIG` est une chaîne JSON. Voici un exemple de cette variable pour un serveur de paramètres :

```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Pour un niveau élevé de TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow cela analysera `TF_CONFIG` spécification de variable et build du cluster pour vous.

Pour niveau inférieur des API principales de TensorFlow pour la formation, analyser le `TF_CONFIG` variable et build le `tf.train.ClusterSpec` dans votre code de formation. Dans [cet exemple](https://aka.ms/aml-notebook-tf-ps),vous allez procéder de la façon suivante dans **votre script d’entraînement** :

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Prise en charge de Keras

[Keras](https://keras.io/) est une API Python réseau de neurones profond populaires, de haut niveau qui prend en charge CNTK, TensorFlow et Theano en tant que serveurs principaux. Si vous utilisez TensorFlow comme serveur principal, Keras est aussi simple qu’y compris un `pip_package` paramètre de constructeur.

L’exemple suivant instancie un [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimateur et la soumet sous une expérience. L’estimateur d’exécute le script de formation de Keras `keras_train.py`. Le travail est exécuté sur un processeur graphique prenant en charge [cible de calcul](how-to-set-up-training-targets.md) avec Keras installé en tant que dépendance via pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Exporter vers ONNX

Pour obtenir une inférence optimisée avec la [ONNX Runtime](concept-onnx.md), vous pouvez convertir votre modèle TensorFlow formé au format ONNX. Reportez-vous à l’[exemple](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Exemples

Vous trouverez des exemples de code de travail pour les exécutions de TensorFlow à nœud unique et distribuées à l’aide de différentes infrastructures sur [notre page GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)