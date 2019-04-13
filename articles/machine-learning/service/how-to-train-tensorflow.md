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
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 78db7d21774750892c831ac220244c54594b78f3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548352"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Former des modèles TensorFlow et Keras avec le service Azure Machine Learning

Pour un entraînement de réseau neuronal profond à l’aide de TensorFlow, Azure Machine Learning fournit une classe `TensorFlow`personnalisée de l’`Estimator`. Le SDK Azure [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator (ne pas à être de pair avec le [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) classe) vous permet de soumettre des travaux de formation TensorFlow pour les exécutions de nœud unique et distribuées sur Azure calcul.

## <a name="single-node-training"></a>Entraînement à nœud unique
L’entraînement avec l’estimateur `TensorFlow` est similaire à celui qui utilise [l’`Estimator` de base](how-to-train-ml-models.md). Ainsi, vous devez d’abord lire l’article sur les procédures pour bien comprendre les concepts qui y sont présentés.
  
Pour exécuter une tâche TensorFlow, instanciez un objet `TensorFlow`. Vous devez déjà avoir créé votre objet de [cible de calcul](how-to-set-up-training-targets.md#amlcompute) `compute_target`.

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

Ici, nous spécifions les paramètres suivants au constructeur TensorFlow :

Paramètre | Description
--|--
`source_directory` | Répertoire local qui contient l’ensemble du code nécessaire à la tâche d’entraînement. Ce dossier est copié de votre ordinateur local vers la cible de calcul distante
`script_params` | Dictionnaire en spécifiant les arguments de ligne de commande à votre script de formation `entry_script`, sous la forme de < argument de ligne de commande, valeur > paires.  Pour spécifier un indicateur verbose dans `script_params`, utilisez `<command-line argument, "">`.
`compute_target` | Cible de calcul à distance sur laquelle votre script de formation s’exécute, ici un cluster de calcul Azure Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute))
`entry_script` | Chemin de fichier (relatif à `source_directory`) du script d’entraînement à exécuter sur la cible de calcul distante. Ce fichier et tous les autres fichiers dont il dépend doivent se trouver dans ce dossier
`conda_packages` | Liste des packages Python à installer via conda et dont a besoin votre script d’entraînement. Dans ce cas, le script d’entraînement utilise `sklearn` pour charger les données. Vous devez donc spécifier que ce package doit être installé.  Le constructeur a un autre paramètre appelé `pip_packages` que vous pouvez utiliser pour tous les packages pip nécessaires
`use_gpu` | Définissez cet indicateur sur `True` afin d’utiliser le GPU pour l’entraînement. La valeur par défaut est `False`.

Étant donné que vous utilisez l’estimateur TensorFlow, le conteneur utilisé pour l’entraînement inclut par défaut le package TensorFlow et les dépendances associées qui sont nécessaires à l’entraînement sur les processeurs et les GPU.

Ensuite, envoyez la tâche TensorFlow :
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Prise en charge de Keras
[Keras](https://keras.io/) est une API de Python de réseau de neurones profond haut niveau populaires qui prend en charge TensorFlow, CNTK ou Theano en tant que serveurs principaux. Si vous utilisez TensorFlow comme serveur principal, vous pouvez facilement utiliser l’estimateur de TensFlow pour former un modèle de Keras. Voici un exemple d’un estimateur TensorFlow avec Keras ajoutés à ce dernier :

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
Le constructeur d’estimateur TensorFlow ci-dessus indique à service Azure Machine Learning pour installer Keras via pip pour l’environnement d’exécution. Et votre `keras_train.py` pouvez ensuite importer l’API Keras pour former un modèle de Keras. Pour obtenir un exemple complet, explorez [ce bloc-notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb).

## <a name="distributed-training"></a>Entraînement distribué
L’estimateur TensorFlow vous permet également d’entraîner vos modèles à grande échelle sur les clusters GPU et UC des machines virtuelles Azure. Vous pouvez facilement exécuter un entraînement TensorFlow distribué avec quelques appels d’API, pendant qu’Azure Machine Learning gère en arrière-plan l’ensemble de l’infrastructure et de l’orchestration qui sont nécessaires pour effectuer ces charges de travail.

Azure Machine Learning prend en charge deux méthodes d’entraînement distribué dans TensorFlow :
* L’entraînement distribué MPI qui utilise le framework [Horovod](https://github.com/uber/horovod)
* L’entraînement [distribué TensorFlow](https://www.tensorflow.org/deploy/distributed) natif via la méthode de serveur de paramètres

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) est un framework open source ring-allreduce pour l’entraînement distribué, développé par Uber.

Pour exécuter un entraînement distribué TensorFlow à l’aide du framework Horovod, créez l’objet TensorFlow de la façon suivante :

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

Le code ci-dessus expose les nouveaux paramètres suivants au constructeur TensorFlow :

Paramètre | Description | Default
--|--|--
`node_count` | Nombre de nœuds à utiliser pour la tâche d’entraînement. | `1`
`process_count_per_node` | Nombre de processus (ou « workers ») à exécuter sur chaque nœud.|`1`
`distributed_backend` | Back-end pour lancer l’entraînement distribué, que l’estimateur propose via MPI. Si vous souhaitez exécuter parallèle ou distribuée de formation (par exemple, `node_count`> 1 ou `process_count_per_node`> 1, ou les deux) avec MPI (et Horovod), définissez `distributed_backend='mpi'`. L’implémentation MPI utilisée par Azure Machine Learning est [Open MPI](https://www.open-mpi.org/). | `None`

L’exemple ci-dessus exécute l’entraînement distribué avec deux workers, un sur chaque nœud.

Horovod et ses dépendances doit être installés pour vous, afin de pouvoir l’importer dans votre script de formation `train.py` comme suit :

```Python
import tensorflow as tf
import horovod
```

Enfin, envoyez la tâche TensorFlow :
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Serveur de paramètres
Vous pouvez également exécuter l’entraînement [natif distribué TensorFlow](https://www.tensorflow.org/deploy/distributed), qui utilise le modèle de serveur de paramètres. Dans cette méthode, vous effectuez un entraînement sur un cluster de serveurs de paramètres et de workers. Les workers calculent les dégradés pendant l’entraînement, alors que les serveurs de paramètres agrègent les dégradés.

Construisez l’objet TensorFlow :

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Les paramètres suivants du constructeur TensorFlow du code ci-dessus méritent une attention particulière :

Paramètre | Description | Default
--|--|--
`worker_count` | Nombre de workers. | `1`
`parameter_server_count` | Nombre de serveurs de paramètres. | `1`
`distributed_backend` | Back-end à utiliser pour l’entraînement distribué. Pour effectuer un entraînement distribué via un serveur de paramètres, définissez `distributed_backend='ps'`. | `None`

#### <a name="note-on-tfconfig"></a>Remarque sur `TF_CONFIG`
Vous aurez également besoin des adresses réseau et des ports du cluster pour [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec). Par conséquent, Azure Machine Learning définit la variable d’environnement `TF_CONFIG`.

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

Si vous utilisez un niveau élevé de TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow cela analysera `TF_CONFIG` spécification de variable et build du cluster pour vous. 

Si vous utilisez les API de base de TensorFlow pour l’entraînement, vous devrez analyser la variable `TF_CONFIG` et créer le `tf.train.ClusterSpec` vous-même dans le code de votre entraînement. Dans [cet exemple](https://aka.ms/aml-notebook-tf-ps),vous allez procéder de la façon suivante dans **votre script d’entraînement** :

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Une fois que vous avez terminé d’écrire votre script d’entraînement et que vous avez créé l’objet TensorFlow, vous pouvez envoyer votre tâche d’entraînement :
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Exemples

Explorez les différents [blocs-notes sur une formation approfondie échelonnée sur Github](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
