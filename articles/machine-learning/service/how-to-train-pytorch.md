---
title: Former et enregistrer des modèles PyTorch
titleSuffix: Azure Machine Learning service
description: Cet article vous explique comment entraîner et inscrire un modèle PyTorch à l’aide d’Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: d9c953eeecedf14a8f3fae43c5d4713252d58b4c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840083"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Entraîner et inscrire des modèles PyTorch à l’échelle avec Azure Machine Learning service

Cet article vous explique comment entraîner et inscrire un modèle PyTorch à l’aide d’Azure Machine Learning service. Il se base sur un [didacticiel d’apprentissage du transfert PyTorch](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) qui crée un classifieur de réseau neuronal profond (DNN) pour des images de poules et de dindes.

[PyTorch](https://pytorch.org/) est une infrastructure de calcul open source couramment utilisée pour créer des réseaux neuronaux profonds (DNN). Avec Azure Machine Learning service, vous pouvez rapidement faire monter en charge des tâches de formation open source à l’aide de ressources de calcul cloud élastiques. Vous pouvez également suivre vos sessions de formation, les modèles de version, les modèles de déploiement et bien plus encore.

Que vous développiez un modèle PyTorch de A à Z ou importiez un modèle existant dans le cloud, Azure Machine Learning service peut vous aider à créer des modèles prêts pour la production.

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :

 - Machine virtuelle de Notebook Azure Machine Learning : pas d’installation ou de téléchargement nécessaire

    - Effectuez un [démarrage rapide du notebook informatique](quickstart-run-cloud-notebook.md) pour créer un serveur de notebook dédié dans lequel le kit de développement logiciel (SDK) et l’exemple de référentiel auront été préchargés.
    - Dans le dossier des exemples du serveur de notebook, recherchez un notebook terminé et développé en accédant à ce répertoire : le dossier **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-pytorch**. 
 
 - Votre propre serveur de notebooks Jupyter

    - [Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python](setup-create-workspace.md#sdk)
    - [Créer un fichier de configuration d’espace de travail](setup-create-workspace.md#write-a-configuration-file)
    - [Télécharger les exemples de fichiers de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’apprentissage via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’apprentissage.

### <a name="import-packages"></a>Importer des packages

Tout d’abord, importez les bibliothèques Python nécessaires.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Initialiser un espace de travail

L’[espace de travail Azure Machine Learning service](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Création d'une expérience

Créez une expérience et un dossier pour stocker vos scripts de formation. Dans cet exemple, créez une expérience appelée « pytorch-birds ».

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Obtenir les données

Le jeu de données se compose de 120 images de formation par dindes et poules, avec 100 images de validations par classe. Nous téléchargeons et extrayons le jeu de données dans le cadre de notre script de formation `pytorch_train.py`. Les images sont un sous-ensemble du [Jeu de données Open Images v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Préparer des scripts de formation

Dans ce didacticiel, le script de formation, `pytorch_train.py`, est déjà fourni. En pratique, vous pouvez prendre n’importe quel script de formation tel quel, et l’exécuter avec Azure Machine Learning service.

Chargez le script de formation Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Toutefois, si vous voulez utiliser les fonctionnalités de métriques et de suivi d’Azure Machine Learning service, vous devez ajouter un peu de code dans votre script de formation. Vous trouverez des exemples de métriques et de suivi dans `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Créer une cible de calcul

Créez une cible de calcul sur laquelle vous exécuterez votre tâche PyTorch. Dans cet exemple, créez un cluster de calcul Azure Machine Learning compatible avec le GPU.

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

## <a name="create-a-pytorch-estimator"></a>Créer un estimateur PyTorch

L’[estimateur PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) fournit un moyen pour lancer simplement la tâche de formation de PyTorch sur une cible de calcul.

L’estimateur PyTorch est implémentée via la classe générique [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), qui peut être utilisée pour prendre en charge n’importe quelle infrastructure. Pour plus d’informations sur la formation des modèles à l’aide de l’estimateur générique, consultez [Former des modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

Si l’exécution de votre script de formation exige des packages PIP ou Conda supplémentaires, vous pouvez installer les packages sur l’image docker obtenue en transmettant leurs noms via les arguments `pip_packages` et `conda_packages`.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur PyTorch. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **Running** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="register-or-download-a-model"></a>Inscrire ou télécharger un modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Vous pouvez aussi télécharger une copie locale du modèle à l’aide de l’objet d’exécution. Dans le script de formation `pytorch_train.py`, un objet de sauvegarde PyTorch conserve le modèle dans un dossier local (local dans la cible de calcul). Vous pouvez utiliser l’objet d’exécution pour télécharger une copie.

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

L’estimateur [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) prend également en charge la formation distribuée sur les clusters de l’UC et du GPU. Vous pouvez facilement exécuter des tâches distribuées PyTorch et Azure Machine Learning service gère l’orchestration à votre place.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) est une infrastructure open source réduite pour la formation distribuée, développée par Uber. Elle offre un accès facile vers des tâches distribuées PyTorch du GPU.

Pour utiliser Horovod, spécifiez un objet [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) pour le paramètre `distributed_training` dans le constructeur PyTorch. Ce paramètre s’assure que la bibliothèque Horovod est installée et que vous pouvez l’utiliser dans votre script de formation.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod et ses dépendances sont installés automatiquement, ce qui vous permet de les importer dans votre script d’entraînement `train.py` de la façon suivante :

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exporter dans ONNX

Pour optimiser l’inférence avec le [Runtime ONNX](concept-onnx.md), convertissez votre modèle PyTorch formé au format ONNX. L’inférence, ou scoring du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production. Pour obtenir un exemple, consultez le [tutoriel](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez entraîné et inscrit un modèle PyTorch sur Azure Machine Learning service. Pour savoir comment déployer un modèle, passez à notre article relatif aux modèles de déploiement.

> [!div class="nextstepaction"]
> [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)
* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
