---
title: Entraîner des modèles Machine Learning avec scikit-Learn
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter vos scripts de formation scikit-Learn à l’échelle de l’entreprise à l’aide de la classe SKlearn estimateur d’Azure Machine Learning. Les exemples de scripts classifient les images de fleurs d'iris afin de créer un modèle d'apprentissage automatique basé sur le jeu de données iris de scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 707c6d99d4c5f4335ff771bdd916b2ee37092604
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710060"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Créer des modèles scikit-learn à l’échelle avec Azure Machine Learning

Dans cet article, découvrez comment exécuter vos scripts de formation scikit-Learn à l’échelle de l’entreprise à l’aide de la classe [SKlearn estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) d’Azure Machine Learning. 

Dans cet article, les exemples de scripts classifient les images de fleurs d'iris afin de créer un modèle d'apprentissage automatique basé sur le [jeu de données iris](https://archive.ics.uci.edu/ml/datasets/iris) de scikit-learn.

Que vous soyez en train d’entraîner un modèle Machine Learning scikit-Learn à partir de zéro ou que vous transfériez un modèle existant dans le cloud, vous pouvez utiliser Azure Machine Learning pour faire mettre à l’échelle les travaux d’apprentissage open source à l’aide de ressources de calcul élastiques dans le cloud. Vous pouvez créer, déployer, mettre à jour et surveiller des modèles de niveau production avec Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :
 - Machine virtuelle de Notebook Azure Machine Learning : pas d’installation ou de téléchargement nécessaire

    - Suivre le [Tutoriel : Configurez l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur Notebook dédié préchargé avec le kit de développement logiciel (SDK) et l’exemple de référentiel.
    - Dans le dossier des exemples d’entraînement du serveur de notebooks, recherchez un notebook finalisé et développé en accédant à ce répertoire : dossier **how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn**.

 - Votre propre serveur de notebooks Jupyter

    - [Installez le kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Créez un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).
    - Télécharger le jeu de données et l’exemple de fichier de script 
        - [jeu de données iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook comprend une section plus détaillée couvrant le paramétrage des hyperparamètres intelligents et la récupération du meilleur modèle par les métriques primaires.

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

L’[espace de travail Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Créer une expérience d’apprentissage automatique

Créez une expérience et un dossier pour stocker vos scripts d’apprentissage. Dans cet exemple, créez une expérience appelée « sklearn-iris ».

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Préparer un script d’apprentissage

Dans ce tutoriel, le script d’entraînement **train_iris.py** est déjà fourni. Dans la pratique, vous devez être capable de prendre n’importe quel script d’apprentissage personnalisé et de l’exécuter avec Azure Machine Learning sans avoir à modifier votre code.

Pour utiliser les fonctionnalités de suivi et de mesures d’Azure Machine Learning, ajoutez une petite quantité de code Azure Machine Learning à l’intérieur de votre script d’apprentissage.  Le script d’entraînement **train_iris.py** montre comment journaliser certaines métriques dans votre exécution Azure ML en utilisant l’objet `Run` dans le script.

Le script d’entraînement fourni utilise les exemples de données de la fonction `iris = datasets.load_iris()`.  Pour vos propres données, vous devrez peut-être suivre des étapes telles que [Charger un jeu de données et des scripts](how-to-train-keras.md#data-upload) pour rendre les données disponibles pendant l’apprentissage.

Copiez le script d’entraînement **train_iris.py** dans le répertoire de votre projet.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Créer ou obtenir une cible de calcul

Créez une cible de calcul sur laquelle vous exécuterez votre tâche Scikit-learn. Scikit-learn prend uniquement en charge les calculs d’UC à nœud unique.

Le code suivant crée un calcul managé Azure Machine Learning (AmlCompute) pour votre ressource de calcul de formation à distance. La création d’AmlCompute prend environ cinq minutes. Si un calcul AmlCompute portant ce nom est déjà dans votre espace de travail, ce code ignore le processus de création.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Pour plus d’informations sur les cibles de calcul, consultez l’article [Qu’est-ce qu’une cible de calcul](concept-compute-target.md).

## <a name="create-a-scikit-learn-estimator"></a>Créer un estimateur Scikit-learn

L’[estimateur Scikit-learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fournit un moyen pour lancer simplement la tâche de formation de Scikit-learn sur une cible de calcul. Il est implémenté via la classe [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) qui peut être utilisé pour prendre en charge de la formation d’UC à nœud unique.

Si l’exécution de votre script de formation exige des packages PIP ou Conda supplémentaires, vous pouvez installer les packages sur l’image docker obtenue en transmettant leurs noms via les arguments `pip_packages` et `conda_packages`.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur TensorFlow. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **Running** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : Le dossier ./outputs de l’exécution est copiée dans l’historique des exécutions.

## <a name="save-and-register-the-model"></a>Télécharger et enregistrer le modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’enregistrer et l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

Ajoutez le code suivant à votre script de formation, train_iris.py, pour enregistrer le modèle. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Inscrivez le modèle sur votre espace de travail avec le code suivant.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Étapes suivantes


Dans cet article, vous avez entraîné et inscrit un modèle Keras sur Azure Machine Learning. Pour savoir comment déployer un modèle, passez à notre article relatif aux modèles de déploiement.

> [!div class="nextstepaction"]
> [Comment et où déployer des modèles ?](how-to-deploy-and-where.md)
* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
* [Architecture de référence de la formation du Deep Learning distribué dans Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
