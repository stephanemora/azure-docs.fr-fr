---
title: Entraîner et inscrire des modèles Chainer
titleSuffix: Azure Machine Learning service
description: Cet article explique comment entraîner et inscrire un modèle Chainer à l’aide d’Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 5057a8cf6c8769761a1a1a9f513ec46d152289ab
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488674"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Entraîner et inscrire des modèles Chainer à l’échelle avec Azure Machine Learning service

Cet article explique comment entraîner et inscrire un modèle Chainer à l’aide d’Azure Machine Learning service. Il utilise le jeu de données populaire [MNIST](http://yann.lecun.com/exdb/mnist/) pour classer les nombres manuscrits à l’aide d’un réseau neuronal profond (DNN) construit à l’aide de la [bibliothèque Python Chainer](https://Chainer.org) s’exécutant par-dessus [numpy](https://www.numpy.org/).

Chainer est une API de réseau neuronal de haut niveau capable de s’exécuter par-dessus d’autres infrastructures DNN populaires afin de simplifier le développement. Azure Machine Learning service vous permet de rapidement faire monter en charge des tâches de formation à l’aide de ressources de calcul cloud élastiques. Vous pouvez également suivre vos sessions de formation, contrôler les versions des modèles, déployer les modèles, et bien plus encore.

Que vous développiez un modèle Chainer de A à Z ou importiez un modèle existant dans le cloud, Azure Machine Learning service peut vous aider à créer des modèles prêts pour la production.

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :

- Machine virtuelle de Notebook Azure Machine Learning : pas d’installation ou de téléchargement nécessaire

    - Suivez le [guide de démarrage rapide des notebooks cloud](quickstart-run-cloud-notebook.md) pour créer un serveur de notebooks dédié et préchargé avec le Kit de développement logiciel (SDK) et l’exemple de dépôt.
    - Dans le dossier des exemples du serveur de notebooks, recherchez un notebook terminé et des fichiers dans le dossier **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer**.  Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

- Votre propre serveur de notebooks Jupyter

    - [Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python](setup-create-workspace.md#sdk)
    - [Créer un fichier de configuration d’espace de travail](setup-create-workspace.md#write-a-configuration-file)
    - Télécharger l’exemple de fichier de script [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’apprentissage via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’apprentissage.

### <a name="import-packages"></a>Importer des packages

Commencez par importer la bibliothèque Python azureml.core et afficher le numéro de version.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Initialiser un espace de travail

L’[espace de travail Azure Machine Learning service](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Créer un répertoire de projet
Créez un répertoire destiné à accueillir tout le code nécessaire de votre machine locale auquel vous devrez accéder sur la ressource distante. Cela inclut le script d’apprentissage et d’autres fichiers dont il dépend.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Préparer un script d’apprentissage

Dans ce didacticiel, le script d’apprentissage **chainer_mnist.py** est fourni. Dans la pratique, vous devez être capable de prendre n’importe quel script d’apprentissage personnalisé et de l’exécuter avec Azure Machine Learning sans avoir à modifier votre code.

Pour utiliser les fonctionnalités de suivi et de mesures d’Azure Machine Learning, vous devrez ajouter une petite quantité de code Azure Machine Learning à l’intérieur de votre script d’apprentissage.  Le script d’apprentissage **chainer_mnist.py** montre comment consigner des mesures dans votre exécution Azure Machine Learning. Pour ce faire, vous accédez à l’objet `Run` Azure Machine Learning au sein du script.

Copiez le script d’apprentissage **chainer_mnist.py** dans votre répertoire de projet.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Création d'une expérience

Créez une expérience et un dossier pour stocker vos scripts d’apprentissage. Dans cet exemple, créez une expérience appelée « chainer-mnist ».

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Créer ou obtenir une cible de calcul

Vous avec besoin d’une [cible de calcul](concept-compute-target.md) pour l’apprentissage de votre modèle. Dans ce didacticiel, vous allez utiliser un calcul managé Azure Machine Learning (AmlCompute) pour votre ressource de calcul d’apprentissage à distance.

**La création d’AmlCompute prend environ cinq minutes**. Si un calcul AmlCompute portant ce nom est déjà dans votre espace de travail, ce code ignore le processus de création.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Pour plus d’informations sur les cibles de calcul, consultez l’article [Qu’est-ce qu’une cible de calcul](concept-compute-target.md).

## <a name="create-a-chainer-estimator"></a>Créer un estimateur Chainer

L’[estimateur Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) offre un moyen simple de lancer des travaux d’apprentissage Chainer sur votre cible de calcul.

L’estimateur Chainer est implémenté via la classe générique [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), qui peut être utilisée pour prendre en charge n’importe quelle infrastructure. Pour plus d’informations sur l’apprentissage des modèles à l’aide de l’estimateur générique, voir [Effectuer l’apprentissage de modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur Chainer. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **Exécution** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : Le dossier ./outputs de l’exécution est copiée dans l’historique des exécutions.

## <a name="save-and-register-the-model"></a>Télécharger et enregistrer le modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’enregistrer et l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

Ajoutez le code suivant à votre script d’apprentissage, **chainer_mnist.py**, pour enregistrer le modèle. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Inscrivez le modèle sur votre espace de travail avec le code suivant.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez effectué l’apprentissage d’un modèle Chainer sur Azure Machine Learning service. 

* Pour savoir comment déployer un modèle, passez à notre article relatif aux [Modèles de déploiement](how-to-deploy-and-where.md).

* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
