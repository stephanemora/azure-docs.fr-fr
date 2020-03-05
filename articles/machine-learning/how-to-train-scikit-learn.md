---
title: Entraîner des modèles Machine Learning scikit-learn
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter vos scripts de formation scikit-learn à l’échelle de l’entreprise à l’aide de la classe Estimateur SKlearn d’Azure Machine Learning. Les exemples de scripts classifient les images de fleurs d'iris afin de créer un modèle d'apprentissage automatique basé sur le jeu de données iris de scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: d61e33568297e6f72aca0ab736f8a14f1758ffa1
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255125"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Créer des modèles scikit-learn à l’échelle avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez comment exécuter vos scripts de formation scikit-learn à l’échelle de l’entreprise à l’aide de la classe [Estimateur SKlearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) d’Azure Machine Learning. 

Dans cet article, les exemples de scripts classifient les images de fleurs d'iris afin de créer un modèle d'apprentissage automatique basé sur le [jeu de données iris](https://archive.ics.uci.edu/ml/datasets/iris) de scikit-learn.

Que vous soyez en train d’entraîner un modèle Machine Learning scikit-Learn à partir de zéro ou que vous transfériez un modèle existant dans le cloud, vous pouvez utiliser Azure Machine Learning pour faire mettre à l’échelle les travaux d’apprentissage open source à l’aide de ressources de calcul élastiques dans le cloud. Vous pouvez créer, déployer, mettre à jour et surveiller des modèles de niveau production avec Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :
 - Instance de calcul Azure Machine Learning : pas de téléchargement ni d’installation nécessaire

    - Suivre le [Tutoriel : Configurez l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur Notebook dédié préchargé avec le kit de développement logiciel (SDK) et l’exemple de référentiel.
    - Dans le dossier des exemples d’entraînement du serveur de notebooks, recherchez un notebook finalisé et développé en accédant à ce répertoire : dossier **how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn**.

 - Votre propre serveur de notebooks Jupyter

    - [Installez le kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Créer un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).
    - Télécharger le jeu de données et l’exemple de fichier de script 
        - [jeu de données iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
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

Pour utiliser les fonctionnalités de suivi et de mesures d’Azure Machine Learning, ajoutez une petite quantité de code Azure Machine Learning à l’intérieur de votre script de formation.  Le script d’entraînement **train_iris.py** montre comment journaliser certaines métriques dans votre exécution Azure ML en utilisant l’objet `Run` dans le script.

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


Pour plus d’informations sur la personnalisation de votre environnement Python, consultez [Créer et gérer des environnements pour l’entraînement et le déploiement](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur TensorFlow. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **En cours d’exécution** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="save-and-register-the-model"></a>Télécharger et enregistrer le modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’enregistrer et l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

Ajoutez le code suivant à votre script de formation, train_iris.py, pour enregistrer le modèle. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Inscrivez le modèle sur votre espace de travail avec le code suivant. En spécifiant les paramètres `model_framework`, `model_framework_version` et `resource_configuration`, le modèle de déploiement sans code devient disponible. Cela vous permet de déployer directement votre modèle en tant que service web à partir du modèle inscrit, et l’objet `ResourceConfiguration` définit la ressource de calcul pour le service web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Déploiement

Le modèle que vous venez d’inscrire peut être déployé exactement de la même façon que n’importe quel autre modèle inscrit dans Azure Machine Learning, quel que soit l’estimateur utilisé pour l’entraînement. Le guide pratique de déploiement contient une section sur l’inscription des modèles, mais vous pouvez passer directement à la [création d’une cible de calcul](how-to-deploy-and-where.md#choose-a-compute-target) pour le déploiement, puisque vous disposez déjà d’un modèle inscrit.

### <a name="preview-no-code-model-deployment"></a>(Préversion) Modèle de déploiement sans code

Au lieu de la route de déploiement traditionnelle, vous pouvez également utiliser la fonctionnalité de déploiement sans code (préversion) pour scikit-learn. Le modèle de déploiement sans code est pris en charge pour tous les types de modèles scikit-learn intégrés. En inscrivant votre modèle comme indiqué ci-dessus avec les paramètres `model_framework`, `model_framework_version` et `resource_configuration`, vous pouvez simplement utiliser la fonction statique `deploy()` pour déployer votre modèle.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

REMARQUE :  Ces dépendances sont incluses dans le conteneur d’inférence scikit-learn prédéfini.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Le [guide pratique](how-to-deploy-and-where.md) complet couvre de manière plus approfondie le déploiement dans Azure Machine Learning.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez entraîné et inscrit un modèle scikit-learn, et vous avez découvert les options de déploiement. Consultez ces autres articles pour en savoir plus sur Azure Machine Learning.

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Architecture de référence de la formation du Deep Learning distribué dans Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
