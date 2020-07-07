---
title: Entraîner des modèles PyTorch de deep learning
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter vos scripts de formation PyTorch à l’échelle de l’entreprise à l’aide de la classe d’estimateur PyTorch de Azure Machine Learning.  Les exemples de scripts classifient des images de poulets et de dindes pour créer un réseau neural d’apprentissage profond basé sur le didacticiel sur le transfert d’apprentissage de PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: df49a8e5a183f56c8584e9d85fe9cfa73bc17491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433826"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Former des modèles d’apprentissage profond Pytorch à l’échelle avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez comment exécuter vos scripts de formation [PyTorch](https://pytorch.org/) à l’échelle de l’entreprise à l'aide de la classe d’[estimateur PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) de Azure Machine Learning.  

Les exemples de scripts dans cet article classifient des images de poulets et de dindes pour créer un réseau neural d’apprentissage profond basé sur le [didacticiel](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) sur le transfert d’apprentissage de PyTorch. 

Que vous soyez en train de former un modèle PyTorch d’apprentissage profond ou que vous déposez un modèle existant dans le Cloud, vous pouvez utiliser Azure Machine Learning pour effectuer un scale-out des tâches de formation Open source à l’aide des ressources de calcul de Cloud élastique. Vous pouvez créer, déployer, mettre à jour et surveiller des modèles de niveau production avec Azure Machine Learning. 

En savoir plus la [comparaison entre apprentissage profond et apprentissage automatique](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :

- Instance de calcul Azure Machine Learning : pas de téléchargement ni d’installation nécessaire

    - Suivre le [Tutoriel : Configurer l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur de notebook dédié préchargé avec le kit SDK et l’exemple de dépôt.
    - Dans le dossier des exemples d’apprentissage profond sur le serveur de notebook, recherchez un notebook terminé et développé en accédant à ce répertoire : le dossier **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-pytorch**. 
 
 - Votre propre serveur de notebooks Jupyter

    - [Installez le kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Créer un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).
    - [Télécharger les exemples de fichiers de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

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

L’[espace de travail Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Créer une expérience d’apprentissage profond

Créez une expérience et un dossier pour stocker vos scripts d’apprentissage. Dans cet exemple, créez une expérience appelée « pytorch-birds ».

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Obtenir les données

Le jeu de données se compose de 120 images de formation par dindes et poules, avec 100 images de validations par classe. Nous téléchargeons et extrayons le jeu de données dans le cadre de notre script de formation `pytorch_train.py`. Les images sont un sous-ensemble du [Jeu de données Open Images v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Préparer des scripts de formation

Dans ce didacticiel, le script de formation, `pytorch_train.py`, est déjà fourni. En pratique, vous pouvez prendre n’importe quel script d’entraînement tel quel et l’exécuter avec Azure Machine Learning.

Chargez le script de formation Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Toutefois, si vous voulez utiliser les fonctionnalités de métriques et de suivi d’Azure Machine Learning, vous devez ajouter un peu de code dans votre script d’entraînement. Vous trouverez des exemples de métriques et de suivi dans `pytorch_train.py`.

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

L’estimateur PyTorch est implémentée via la classe générique [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), qui peut être utilisée pour prendre en charge n’importe quelle infrastructure. Pour plus d’informations sur l’apprentissage des modèles à l’aide de l’estimateur générique, voir [Effectuer l’apprentissage de modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

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

Pour plus d’informations sur la personnalisation de votre environnement Python, consultez [Créer et gérer des environnements pour l’entraînement et le déploiement](how-to-use-environments.md).

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur PyTorch. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **En cours d’exécution** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="register-or-download-a-model"></a>Inscrire ou télécharger un modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Le modèle que vous venez d’inscrire est déployé exactement de la même façon que n’importe quel autre modèle inscrit dans Azure Machine Learning, quel que soit l’estimateur utilisé pour la formation. Le guide pratique de déploiement contient une section sur l’inscription des modèles, mais vous pouvez passer directement à la [création d’une cible de calcul](how-to-deploy-and-where.md#choose-a-compute-target) pour le déploiement, puisque vous disposez déjà d’un modèle inscrit.

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

L’estimateur [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) prend également en charge la formation distribuée sur les clusters de l’UC et du GPU. Vous pouvez facilement exécuter des tâches distribuées PyTorch et Azure Machine Learning gère l’orchestration à votre place.

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

Pour optimiser l’inférence avec le [Runtime ONNX](concept-onnx.md), convertissez votre modèle PyTorch formé au format ONNX. L’inférence, ou notation du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production. Pour obtenir un exemple, consultez le [tutoriel](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez entraîné et inscrit un réseau neuronal Deep Learning à l’aide de PyTorch sur Azure Machine Learning. Pour savoir comment déployer un modèle, passez à notre article relatif aux modèles de déploiement.

> [!div class="nextstepaction"]
> [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)
* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
* [Architecture de référence de la formation du Deep Learning distribué dans Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

