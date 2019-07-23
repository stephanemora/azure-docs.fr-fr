---
title: Entraîner et inscrire des modèles Keras exécutés sur TensorFlow
titleSuffix: Azure Machine Learning service
description: Cet article vous explique comment entraîner et inscrire un modèle Keras sur TensorFlow à l’aide d’Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9d405b454d755e0c848e9422c8d4cf6e7c505b68
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840046"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Entraîner et inscrire des modèles Keras à l’échelle avec Azure Machine Learning service

Cet article vous explique comment entraîner et inscrire un modèle Keras intégré à TensorFlow à l’aide d’Azure Machine Learning service. Il utilise le jeu de données populaire [MNIST](http://yann.lecun.com/exdb/mnist/) pour classer les nombres manuscrits à l’aide d’un réseau neuronal profond (DNN) construit à l’aide de la [bibliothèque Python Keras](https://keras.io) s’exécutant par-dessus [TensorFlow](https://www.tensorflow.org/overview).

Keras est une API de réseau neuronal de haut niveau capable de s’exécuter par-dessus d’autres infrastructures DNN populaires afin de simplifier le développement. Azure Machine Learning service vous permet de rapidement faire monter en charge des tâches de formation à l’aide de ressources de calcul cloud élastiques. Vous pouvez également suivre vos sessions de formation, contrôler les versions des modèles, déployer les modèles, et bien plus encore.

Que vous développiez un modèle Keras de A à Z ou importiez un modèle existant dans le cloud, Azure Machine Learning service peut vous aider à créer des modèles prêts pour la production.

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :

 - Machine virtuelle de Notebook Azure Machine Learning : pas d’installation ou de téléchargement nécessaire

     - Effectuez un [démarrage rapide du notebook informatique](quickstart-run-cloud-notebook.md) pour créer un serveur de notebook dédié dans lequel le kit de développement logiciel (SDK) et l’exemple de référentiel auront été préchargés.
    - Dans le dossier des exemples du serveur de notebook, recherchez un notebook terminé et développé en accédant à ce répertoire : le dossier **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-keras**. 
 
 - Votre propre serveur de notebooks Jupyter

     - [Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python](setup-create-workspace.md#sdk)
    - [Créer un fichier de configuration d’espace de travail](setup-create-workspace.md#write-a-configuration-file)
    - [Télécharger les exemples de fichiers de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` et `utils.py`
     
    Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’apprentissage via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’apprentissage.

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

L’[espace de travail Azure Machine Learning service](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Création d'une expérience

Créez une expérience et un dossier pour stocker vos scripts d’apprentissage. Dans cet exemple, créez une expérience appelée « keras-mnist ».

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Charger le jeu de données et les scripts

Le [magasin de données](how-to-access-data.md) est l’emplacement où les données peuvent être stockées. Il est possible d’y accéder en montant ou en copiant des données dans la cible de calcul. Chaque espace de travail fournit un magasin de données par défaut. Chargez les données et les scripts de formation dans le magasin de données afin d’y accéder plus facilement pendant la formation.

1. Téléchargez le jeu de données MNIST localement.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Chargez le jeu de données MNIST dans le magasin de données par défaut.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Chargez le script d’apprentissage Keras `keras_mnist.py` et le fichier d’assistance `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Créer un estimateur TensorFlow et importer Keras

[L’estimateur TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fournit un moyen simple de lancer des travaux d’entraînement TensorFlow sur une cible de calcul. Dans la mesure où Keras s’exécute sur TensorFlow, vous pouvez utiliser l’estimateur TensorFlow et importer la bibliothèque Keras à l’aide de l’argument `pip_packages`.

L’estimateur TensorFlow est implémenté via la classe générique [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), qui peut être utilisée pour prendre en charge n’importe quelle infrastructure. Pour plus d’informations sur l’apprentissage des modèles à l’aide de l’estimateur générique, voir [Effectuer l’apprentissage de modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur TensorFlow. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **Running** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="register-the-model"></a>Inscrire le modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Vous pouvez également télécharger une copie du modèle. Cela peut être utile pour effectuer un travail de validation de modèle supplémentaire localement. Dans le script d’entraînement `mnist-keras.py`, un objet de sauvegarde TensorFlow conserve le modèle dans un dossier local (local dans la cible de calcul). Vous pouvez utiliser l’objet d’exécution pour télécharger une copie à partir du magasin de données.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez entraîné et inscrit un modèle Keras sur Azure Machine Learning service. Pour savoir comment déployer un modèle, passez à notre article relatif aux modèles de déploiement.

> [!div class="nextstepaction"]
> [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)
