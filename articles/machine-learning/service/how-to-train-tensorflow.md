---
title: Former et enregistrer des modèles de TensorFlow
titleSuffix: Azure Machine Learning service
description: Cet article vous montre comment former et enregistrer un modèle TensorFlow à l’aide du service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4a6f9734a7b2b59035efcbb0f4e2d75f47e053be
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515591"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Former et enregistrer des modèles TensorFlow à l’échelle avec le service Azure Machine Learning

Cet article vous montre comment former et enregistrer un modèle TensorFlow à l’aide du service Azure Machine Learning. Nous allons utiliser le fameux [jeu de données MNIST](http://yann.lecun.com/exdb/mnist/) pour classer les chiffres manuscrits à l’aide d’un réseau neuronal profond créé à l’aide du [bibliothèque Python de TensorFlow](https://www.tensorflow.org/overview).

Avec le service Azure Machine Learning, vous pourrez rapidement monter en charge de vos travaux de formation de l’open source à l’aide de ressources de calcul cloud élastique. Vous pourrez également effectuer le suivi de vos exécutions de formation, les modèles de version, de déployer des modèles et bien plus encore.

Si vous développez un modèle TensorFlow à partir de la configuration de bout en bout ou que vous importez un modèle existant dans le cloud, vous pouvez créer des modèles prêts pour la production avec le service Azure Machine Learning.

## <a name="prerequisites"></a>Conditions préalables

- Installer le [Azure Machine Learning SDK pour Python](setup-create-workspace.md#sdk). Facultatif : créer un `config.json` fichier de configuration.
- Téléchargez le [exemples de fichiers de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` et `utils.py`

Vous trouverez également renseigné [version de bloc-notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) de ce guide sur notre page d’exemples Github. Le bloc-notes inclut développées sections couvrant le réglage des hyperparamètres intelligent et déploiement de modèle.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience de formation en chargeant les packages python requis, l’initialisation d’un espace de travail, création d’une expérience et charger les données d’apprentissage et les scripts de formation à l’aide du Kit de développement Python.

### <a name="import-packages"></a>Importer des packages

Tout d’abord, nous allons devoir importez les bibliothèques Python nécessaires.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Initialiser un espace de travail

Le [espace de travail de service Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur pour le service. Il vous offre un emplacement centralisé pour travailler avec tous les artefacts que vous créez. Dans le kit SDK Python, vous pouvez accéder à des artefacts de l’espace de travail en créant un [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objet.

Si vous avez terminé l’étape facultative dans le [section conditions préalables](#prerequisites), vous pouvez utiliser `Workspace.from_config()` pour créer rapidement un objet de l’espace de travail à partir des détails stockées dans le fichier de configuration.

```Python
ws = Workspace.from_config()
```

Vous pouvez également créer explicitement un espace de travail :

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>Création d'une expérience

Créer une expérience et un dossier pour stocker vos scripts de formation. Dans cet exemple, créez une expérience appelée « tf-mnist ».

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Télécharger le jeu de données et des scripts

Le [banque de données](how-to-access-data.md) est l’endroit où données peuvent être stockées et accessibles par monter ou de copie des données sur la cible de calcul. Chaque espace de travail fournit un magasin de données par défaut. Nous allons charger nos données et les scripts d’apprentissage afin qu’ils soient facilement accessibles pendant l’apprentissage.

1. Téléchargez le jeu de données MNIST localement.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Téléchargez le jeu de données MNIST dans le magasin de données par défaut.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Télécharger le script de formation TensorFlow, `tf_mnist.py`et le fichier d’assistance, `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Créer une cible de calcul

Créer une cible de calcul pour exécuter votre tâche TensorFlow. Dans cet exemple, nous créons un cluster de calcul compatibles GPU Azure Machine Learning. Pour obtenir la liste de formation disponible des cibles de calcul, consultez [cet article](how-to-set-up-training-targets.md#compute-targets-for-training)

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="create-a-tensorflow-estimator"></a>Créer un estimateur TensorFlow

Le [TensorFlow estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fournit un moyen simple de lancer un travail de formation TensorFlow sur une cible de calcul. Il crée une image docker qui a TensorFlow installé.

L’estimateur de TensorFlow est implémentée via le générique [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) (classe), qui peut être utilisé pour prendre en charge n’importe quelle infrastructure. Pour plus d’informations sur l’apprentissage des modèles à l’aide de l’estimateur de générique, consultez [former des modèles avec Azure Machine Learning à l’aide d’estimateur](how-to-train-ml-models.md)

Si votre script de formation doit supplémentaire pip ou conda l’exécution de packages, vous pouvez avoir les packages installés sur l’image docker obtenue en passant de leurs noms via la `pip_packages` et `conda_packages` arguments.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Soumettre une exécution

Le [exécuter objet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique d’exécution pendant l’exécution de la tâche et une fois celle-ci terminée.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est exécutée, il parcourt les étapes suivantes :

- **Préparation**: Création d’une image docker en fonction de l’estimateur de TensorFlow. L’image est chargée dans container registry l’espace de travail et mis en cache pour des exécutions ultérieures. Journaux sont également diffusés en continu à l’historique des exécutions et peuvent être affichés pour surveiller la progression.

- **Mise à l’échelle** : Le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds à exécuter l’exécution que ceux actuellement disponibles.

- **Running** : Tous les scripts dans le dossier de script sont téléchargés vers la cible de calcul, magasins de données sont montés ou copiées et l’entry_script est exécutée. Sorties à partir de stdout et. / dossier de journaux sont diffusés en continu à l’historique d’exécution et peut être utilisé pour surveiller l’exécution.

- **Post-traitement** : Le. / sorties du dossier de l’exécution est copiée à l’historique des exécutions.

## <a name="register-or-download-a-model"></a>Inscrire ou télécharger un modèle

Une fois que vous avez formé le modèle, vous pouvez l’inscrire à votre espace de travail. Inscription du modèle vous permet de magasin et la version vos modèles dans votre espace de travail afin de simplifier [gestion et le déploiement de modèle](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Vous pouvez également télécharger une copie locale du modèle à l’aide de l’objet d’exécution. Dans le script de formation `mnist-tf.py`, un objet de veille TensorFlow persiste le modèle dans un dossier local (local à la cible de calcul). Nous pouvons utiliser l’objet d’exécution pour télécharger une copie.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Entraînement distribué

Le [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimateur prend également en charge la formation distribuée entre les clusters GPU et UC. Vous pouvez facilement exécuter des tâches TensorFlow distribuées et Azure Machine Learning service gère l’orchestration pour vous.

Service Azure Machine Learning prend en charge deux méthodes de formation distribuée TensorFlow :

- [En fonction de MPI](https://www.open-mpi.org/) distribué à l’aide de la formation la [Horovod](https://github.com/uber/horovod) framework
- Natif [distributed TensorFlow](https://www.tensorflow.org/deploy/distributed) à l’aide de la méthode de serveur de paramètre

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) est une infrastructure open source pour la formation distribuée développé par Uber. Il offre un accès facile aux travaux de GPU TensorFlow distribuées.

Pour utiliser Horovod, spécifiez `mpi` pour le `distributed_training` paramètre dans le constructeur d’estimateur de TensorFlow. Horovod doit être installé pour vous permettent d’utiliser dans votre script de formation.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Serveur de paramètres

Vous pouvez également exécuter l’entraînement [natif distribué TensorFlow](https://www.tensorflow.org/deploy/distributed), qui utilise le modèle de serveur de paramètres. Dans cette méthode, vous effectuez un entraînement sur un cluster de serveurs de paramètres et de workers. Les workers calculent les dégradés pendant l’entraînement, alors que les serveurs de paramètres agrègent les dégradés.

Pour utiliser la méthode de serveur de paramètre, spécifiez `ps` pour le `distributed_training` paramètre dans le constructeur d’estimateur de TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>Définir des spécifications de cluster 'TF_CONFIG'

Vous devez également les adresses réseau et les ports du cluster pour le [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), alors Azure Machine Learning définit la `TF_CONFIG` variable d’environnement pour vous.

La variable d’environnement `TF_CONFIG` est une chaîne JSON. Voici un exemple de cette variable pour un serveur de paramètres :

```JSON
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

Pour niveau inférieur des API principales de TensorFlow pour la formation, analyser le `TF_CONFIG` variable et build le `tf.train.ClusterSpec` dans votre code de formation.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous formé et enregistré un modèle TensorFlow sur le service Azure Machine Learning. Pour savoir comment déployer un modèle, passez à notre article de déploiement de modèle.

> [!div class="nextstepaction"]
> [Comment et où déployer les modèles](how-to-deploy-and-where.md)
