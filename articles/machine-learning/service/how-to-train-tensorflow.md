---
title: Former un réseau neuronal avec TensorFlow
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter des scripts d’entraînement TensorFlow à grande échelle avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2ce3c8c2d28f75d4c1f33d77c7df9e81eed6a822
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276690"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Créer un modèles de Deep Learning TensorFlow à l’échelle avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article vous explique comment exécuter vos scripts de formation [TensorFlow](https://www.tensorflow.org/overview) à l’échelle à l’aide de la classe d’[estimateur TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) d’Azure Machine Learning. Cet exemple forme et inscrit un modèle TensorFlow pour classer les chiffres manuscrits à l’aide d’un réseau neuronal profond (DNN).

Que vous développiez complètement un modèle TensorFlow ou que vous transmettiez un [modèle existant](how-to-deploy-existing-model.md) dans le cloud, vous pouvez utiliser Azure Machine Learning pour monter en charge les travaux de formation Open source afin de créer, déployer, versionner et surveiller des modèles de niveau production.

En savoir plus la [comparaison entre apprentissage profond et apprentissage automatique](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :

 - Machine virtuelle de Notebook Azure Machine Learning : pas d’installation ou de téléchargement nécessaire

     - Suivre le [Tutoriel : Configurer l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur de notebook dédié préchargé avec le kit SDK et l’exemple de dépôt.
    - Dans le dossier des exemples de deep learning sur le serveur de notebooks, recherchez un notebook finalisé et développé en accédant à ce répertoire : dossier **how-to-use-azureml > ml-frameworks > tensorflow > deployment > train-hyperparameter-tune-deploy-with-tensorflow**. 
 
 - Votre propre serveur de notebooks Jupyter

    - [Installez le kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Créez un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).
    - [Télécharger les exemples de fichiers de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` et `utils.py`
     
    Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’entraînement via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’entraînement.

### <a name="import-packages"></a>Importer des packages

Tout d’abord, importez les bibliothèques Python nécessaires.

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

L’[espace de travail Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Créer une expérience d’apprentissage profond

Créez une expérience et un dossier pour stocker vos scripts d’apprentissage. Dans cet exemple, créez une expérience appelée « tf-mnist ».

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Créer un jeu de données de fichier

Un objet `FileDataset` référence un ou plusieurs fichiers dans votre magasin de données d’espace de travail ou vos URL publiques. Les fichiers peuvent être de n’importe quel format, et la classe vous offre la possibilité de télécharger ou de monter les fichiers dans votre calcul. En créant un `FileDataset`, vous créez une référence à l’emplacement de la source de données. Si vous avez appliqué des transformations au jeu de données, elles seront également stockées dans le jeu de données. Les données restant à leur emplacement existant, aucun coût de stockage supplémentaire n’est encouru. Pour plus d’informations, consultez le guide de [procédures](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) sur le package `Dataset`.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Utilisez la méthode `register()` pour inscrire des jeux de données dans votre espace de travail afin de pouvoir les partager avec d’autres personnes, les réutiliser dans différentes expériences et y faire référence par nom dans votre script d’entraînement.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Créer une cible de calcul

Créez une cible de calcul sur laquelle vous exécuterez votre tâche TensorFlow. Dans cet exemple, créez un cluster de calcul Azure Machine Learning compatible avec le GPU.

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

Pour plus d’informations sur les cibles de calcul, consultez l’article [Qu’est-ce qu’une cible de calcul](concept-compute-target.md).

## <a name="create-a-tensorflow-estimator"></a>Créer un estimateur TensorFlow

[L’estimateur TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fournit un moyen simple de lancer un travail d’entraînement TensorFlow sur une cible de calcul.

L’estimateur TensorFlow est implémenté via la classe générique [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), qui peut être utilisée pour prendre en charge n’importe quelle infrastructure. Pour plus d’informations sur l’apprentissage des modèles à l’aide de l’estimateur générique, voir [Effectuer l’apprentissage de modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

Si l’exécution de votre script d’entraînement exige des packages PIP ou Conda supplémentaires, vous pouvez installer les packages sur l’image Docker obtenue en transmettant leurs noms via les arguments `pip_packages` et `conda_packages`.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

> [!TIP]
> La prise en charge de **Tensorflow 2.0** a été ajoutée à la classe d’estimateur Tensorflow. Pour plus d’informations, consultez ce [billet de blog](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/).

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image Docker est créée en fonction de l’estimateur TensorFlow. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **Running** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="register-or-download-a-model"></a>Inscrire ou télécharger un modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md). En spécifiant les paramètres `model_framework`, `model_framework_version` et `resource_configuration`, le modèle de déploiement sans code devient disponible. Cela vous permet de déployer directement votre modèle en tant que service web à partir du modèle inscrit, et l’objet `ResourceConfiguration` définit la ressource de calcul pour le service web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Vous pouvez aussi télécharger une copie locale du modèle à l’aide de l’objet d’exécution. Dans le script de formation `mnist-tf.py`, un objet de sauvegarde TensorFlow conserve le modèle dans un dossier local (local dans la cible de calcul). Vous pouvez utiliser l’objet d’exécution pour télécharger une copie.

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

L’estimateur [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) prend également en charge la formation distribuée sur les clusters de l’UC et du GPU. Vous pouvez facilement exécuter des tâches distribuées TensorFlow et Azure Machine Learning gère l’orchestration à votre place.

Azure Machine Learning prend en charge deux méthodes d’entraînement distribué dans TensorFlow :

- Entraînement distribué [MPI](https://www.open-mpi.org/) qui utilise le framework [Horovod](https://github.com/uber/horovod)
- L’entraînement [distribué TensorFlow](https://www.tensorflow.org/deploy/distributed) natif à l’aide de la méthode de serveur de paramètres

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) est une infrastructure open source pour la formation distribuée, développée par Uber. Elle offre un accès facile vers des tâches distribuées TensorFlow du GPU.

Pour utiliser Horovod, spécifiez un objet [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) pour le paramètre `distributed_training` dans le constructeur TensorFlow. Ce paramètre s’assure que la bibliothèque Horovod est installée et que vous pouvez l’utiliser dans votre script de formation.

```Python
from azureml.core.runconfig import MpiConfiguration
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

Pour utiliser la méthode serveur de paramètres, spécifiez un objet [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) pour le paramètre `distributed_training` dans le constructeur TensorFlow.

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
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Définir des spécifications de cluster « TF_CONFIG »

Vous avez également besoin des adresses réseau et des ports du cluster pour [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec). Par conséquent, Azure Machine Learning définit la variable d’environnement `TF_CONFIG`.

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

Pour l’API de haut niveau [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) de TensorFlow, TensorFlow analyse la variable `TF_CONFIG` et crée les spécifications du cluster pour vous.

Pour les API de base de TensorFlow pour l’entraînement, analysez la variable `TF_CONFIG` et créer le `tf.train.ClusterSpec` dans votre code d’apprentissage.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deployment"></a>Déploiement

Le modèle que vous venez d’inscrire peut être déployé exactement de la même façon que n’importe quel autre modèle inscrit dans Azure Machine Learning, quel que soit l’estimateur utilisé pour l’entraînement. Le guide pratique de déploiement contient une section sur l’inscription des modèles, mais vous pouvez passer directement à la [création d’une cible de calcul](how-to-deploy-and-where.md#choose-a-compute-target) pour le déploiement, puisque vous disposez déjà d’un modèle inscrit.

### <a name="preview-no-code-model-deployment"></a>(Préversion) Modèle de déploiement sans code

Au lieu de l’itinéraire de déploiement traditionnel, vous pouvez également utiliser la fonctionnalité de déploiement sans code (préversion) pour Tensorflow. En inscrivant votre modèle comme indiqué ci-dessus avec les paramètres `model_framework`, `model_framework_version` et `resource_configuration`, vous pouvez simplement utiliser la fonction statique `deploy()` pour déployer votre modèle.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Le [guide pratique](how-to-deploy-and-where.md) complet décrit le déploiement dans Azure Machine Learning de manière plus approfondie.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez effectué l’apprentissage d’un modèle TensorFlow et l’avez inscrit et vous avez découvert les options de déploiement. Consultez ces autres articles pour en savoir plus sur Azure Machine Learning.

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Architecture de référence de la formation du Deep Learning distribué dans Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
