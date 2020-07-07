---
title: Entraîner des modèles Keras de deep learning
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner et enregistrer un modèle de classification de réseau neuronal profond Keras s’exécutant sur TensorFlow à l’aide d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 14649d3e7bc12205283863f725a902a3cef20290
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433860"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Entraîner et enregistrer un modèle de classification Keras avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article explique comment entraîner et inscrire un modèle de classification Keras basé sur TensorFlow à l’aide d’Azure Machine Learning. Il utilise le jeu de données populaire [MNIST](http://yann.lecun.com/exdb/mnist/) pour classer les nombres manuscrits à l’aide d’un réseau neuronal profond (DNN) construit à l’aide de la [bibliothèque Python Keras](https://keras.io) s’exécutant par-dessus [TensorFlow](https://www.tensorflow.org/overview).

Keras est une API de réseau neuronal de haut niveau capable de s’exécuter par-dessus d’autres infrastructures DNN populaires afin de simplifier le développement. Azure Machine Learning vous permet d’effectuer rapidement un scale-out des tâches d’entraînement à l’aide de ressources de calcul cloud élastiques. Vous pouvez également suivre vos sessions de formation, contrôler les versions des modèles, déployer les modèles, et bien plus encore.

Que vous développiez un modèle Keras de A à Z ou importiez un modèle existant dans le cloud, Azure Machine Learning peut vous aider à créer des modèles prêts pour la production.

Pour plus d’informations sur les différences entre l’apprentissage automatique et l’apprentissage approfondi, consultez l’[article conceptuel](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :

- Instance de calcul Azure Machine Learning : pas de téléchargement ni d’installation nécessaire

     - Suivre le [Tutoriel : Configurer l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur de notebook dédié préchargé avec le kit SDK et l’exemple de dépôt.
    - Dans le dossier des exemples du serveur de notebook, recherchez un notebook terminé et développé en accédant à ce répertoire : le dossier **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-keras**.

 - Votre propre serveur de notebooks Jupyter

    - [Installez le kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Créer un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).
    - [Téléchargez les exemples de fichiers de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` et `utils.py`

    Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’apprentissage via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’apprentissage.

### <a name="import-packages"></a>Importer des packages

Tout d’abord, importez les bibliothèques Python nécessaires.

```Python
import os
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

### <a name="create-an-experiment"></a>Créer une expérience

Créez une expérience appelée « keras-mnist » dans votre espace de travail.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Créer un jeu de données de fichier

Un objet `FileDataset` référence un ou plusieurs fichiers dans votre magasin de données d’espace de travail ou vos URL publiques. Les fichiers peuvent être de n’importe quel format, et la classe vous offre la possibilité de télécharger ou de monter les fichiers dans votre calcul. En créant un `FileDataset`, vous créez une référence à l’emplacement de la source de données. Si vous avez appliqué des transformations au jeu de données, elles seront également stockées dans le jeu de données. Les données restant à leur emplacement existant, aucun coût de stockage supplémentaire n’est encouru. Pour plus d’informations, consultez le guide de [procédures](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) sur le package `Dataset`.

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

Commencez par récupérer les données du magasin de données de l’espace de travail à l’aide de la classe `Dataset`.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

L’estimateur TensorFlow est implémenté via la classe générique [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), qui peut être utilisée pour prendre en charge n’importe quelle infrastructure. En outre, créez un dictionnaire `script_params` qui contient les paramètres d’hyperparamètre DNN. Pour plus d’informations sur l’apprentissage des modèles à l’aide de l’estimateur générique, voir [Effectuer l’apprentissage de modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

- **En cours d’exécution** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="register-the-model"></a>Inscrire le modèle

Une fois que vous avez formé le modèle DNN, vous pouvez l’inscrire dans votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Le modèle que vous venez d’inscrire est déployé exactement de la même façon que n’importe quel autre modèle inscrit dans Azure Machine Learning, quel que soit l’estimateur utilisé pour la formation. Le guide pratique de déploiement contient une section sur l’inscription des modèles, mais vous pouvez passer directement à la [création d’une cible de calcul](how-to-deploy-and-where.md#choose-a-compute-target) pour le déploiement, puisque vous disposez déjà d’un modèle inscrit.

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

Dans cet article, vous avez entraîné et inscrit un modèle Keras sur Azure Machine Learning. Pour savoir comment déployer un modèle, passez à notre article relatif aux modèles de déploiement.

> [!div class="nextstepaction"]
> [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)
* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
* [Architecture de référence de la formation du Deep Learning distribué dans Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
