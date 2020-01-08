---
title: Entraîner des modèles Chainer de deep learning
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter vos scripts de formation PyTorch à l’échelle de l’entreprise à l’aide de la classe d’estimateur Chainer d’Azure Machine Learning.  L’exemple de script classifie les images de chiffres manuscrits pour créer un réseau neural de Deep Learning à l’aide de la bibliothèque python Chainer qui s’exécute sur numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75534117"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Entraîner et inscrire des modèles Chainer à l’échelle avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez comment exécuter vos scripts de formation [Chainer](https://chainer.org/) à l’échelle de l’entreprise à l’aide de la classe d’[estimateur Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) d’Azure Machine Learning. L’exemple de script de formation de cet article utilise le [jeu de données MNIST](http://yann.lecun.com/exdb/mnist/) populaire pour classifier les chiffres manuscrits à l’aide d’un réseau neural profond (DNN) créé à l’aide de la bibliothèque python Chainer qui s’exécute par-dessus [numpy](https://www.numpy.org/).

Que vous soyez en train de former un modèle Chainer d’apprentissage approfondi ou que vous transmettez un modèle existant dans le Cloud, vous pouvez utiliser Azure Machine Learning pour faire évoluer les travaux de formation Open source à l’aide de ressources de calcul élastiques dans le Cloud. Vous pouvez créer, déployer, mettre à jour et surveiller des modèles de niveau production avec Azure Machine Learning. 

En savoir plus la [comparaison entre apprentissage profond et apprentissage automatique](concept-deep-learning-vs-machine-learning.md).

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="prerequisites"></a>Conditions préalables requises

Exécutez ce code sur l’un de ces environnements :

- Instance de calcul Azure Machine Learning : pas de téléchargement ni d’installation nécessaire

    - Suivre le [Tutoriel : Configurer l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur de notebook dédié préchargé avec le kit SDK et l’exemple de dépôt.
    - Dans le dossier d’exemples de deep learning du serveur de notebooks, recherchez un notebook et des fichiers finalisés dans le dossier **how-to-use-azureml > ml-frameworks > chainer > deployment > train-hyperparameter-tune-deploy-with-chainer**.  Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

- Votre propre serveur de notebooks Jupyter

    - [Installez le kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Créer un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).
    - Téléchargez l’exemple de fichier de script [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’apprentissage via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’apprentissage.

### <a name="import-packages"></a>Importer des packages

Commencez par importer la bibliothèque Python azureml.core et par afficher le numéro de version.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Initialiser un espace de travail

L’[espace de travail Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail en lisant le fichier `config.json` créé dans la [section Conditions préalables](#prerequisites) :

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

Pour utiliser les fonctionnalités de suivi et de mesures d’Azure Machine Learning, ajoutez une petite quantité de code Azure Machine Learning à l’intérieur de votre script de formation.  Le script d’apprentissage **chainer_mnist.py** montre comment consigner des mesures dans votre exécution Azure Machine Learning à l’aide de l’objet `Run` dans le script.

Le script d’apprentissage fourni utilise des exemples de données de la fonction de chaînage `datasets.mnist.get_mnist`.  Pour vos propres données, vous devrez peut-être suivre des étapes telles que [Charger un jeu de données et des scripts](how-to-train-keras.md#data-upload) pour rendre les données disponibles pendant l’apprentissage.

Copiez le script d’apprentissage **chainer_mnist.py** dans votre répertoire de projet.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Créer une expérience d’apprentissage profond

Créer une expérience. Dans cet exemple, créez une expérience appelée « chainer-mnist ».

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Créer ou obtenir une cible de calcul

Vous avez besoin d’une [cible de calcul](concept-compute-target.md) pour l’apprentissage de votre modèle. Dans cet exemple, vous allez utiliser un calcul managé Azure Machine Learning (AmlCompute) pour votre ressource de calcul d’apprentissage à distance.

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

- **Préparation** : une image docker est créée en fonction de l’estimateur Chainer. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **En cours d’exécution** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="save-and-register-the-model"></a>Télécharger et enregistrer le modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’enregistrer et l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).


Après avoir terminé la formation du modèle, inscrivez le modèle sur votre espace de travail avec le code suivant.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Le modèle que vous venez d’inscrire est déployé exactement de la même façon que n’importe quel autre modèle inscrit dans Azure Machine Learning, quel que soit l’estimateur utilisé pour la formation. Le guide pratique de déploiement contient une section sur l’inscription des modèles, mais vous pouvez passer directement à la [création d’une cible de calcul](how-to-deploy-and-where.md#choose-a-compute-target) pour le déploiement, puisque vous disposez déjà d’un modèle inscrit.

Vous pouvez également télécharger une copie du modèle. Cela peut être utile pour effectuer un travail de validation de modèle supplémentaire localement. Dans le script d’entraînement `chainer_mnist.py`, un objet de sauvegarde conserve le modèle dans un dossier local (local dans la cible de calcul). Vous pouvez utiliser l’objet d’exécution pour télécharger une copie à partir du magasin de données.

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

Dans cet article, vous avez entraîné et inscrit un réseau neuronal Deep Learning à l’aide de Chainer sur Azure Machine Learning. Pour savoir comment déployer un modèle, passez à notre article relatif aux [modèles de déploiement](how-to-deploy-and-where.md).

* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)

* [Consulter notre architecture de référence de la formation du Deep Learning distribué dans Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
