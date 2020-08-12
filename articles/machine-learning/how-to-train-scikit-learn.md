---
title: Entraîner des modèles Machine Learning scikit-learn
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter vos scripts de formation scikit-learn sur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: dfe3d0e7bf0d291807a5a051f834753c7816801a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320882"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Créer des modèles scikit-learn à l’échelle avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez comment exécuter vos scripts de formation scikit-learn sur Azure Machine Learning.

Dans cet article, les exemples de scripts classifient les images de fleurs d'iris afin de créer un modèle d'apprentissage automatique basé sur le [jeu de données iris](https://archive.ics.uci.edu/ml/datasets/iris) de scikit-learn.

Que vous soyez en train d’entraîner un modèle Machine Learning scikit-Learn à partir de zéro ou que vous transfériez un modèle existant dans le cloud, vous pouvez utiliser Azure Machine Learning pour effectuer un scale-out des travaux d’apprentissage open source à l’aide de ressources de calcul élastiques dans le cloud. Vous pouvez créer, déployer, mettre à jour et surveiller des modèles de niveau production avec Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :
 - Instance de calcul Azure Machine Learning : pas de téléchargement ni d’installation nécessaire

    - Suivre le [Tutoriel : Configurez l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur Notebook dédié préchargé avec le kit de développement logiciel (SDK) et l’exemple de référentiel.
    - Dans le dossier des exemples d’entraînement du serveur de notebooks, recherchez un notebook finalisé et développé en accédant à ce répertoire : dossier **how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn**.

 - Votre propre serveur de notebooks Jupyter

    - [Installez le kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Créer un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’apprentissage via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’apprentissage.

### <a name="initialize-a-workspace"></a>Initialiser un espace de travail

L’[espace de travail Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>Préparer les scripts

Dans ce tutoriel, le script d’entraînement **train_iris.py** est déjà fourni [ici](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). Dans la pratique, vous devez être capable de prendre n’importe quel script d’apprentissage personnalisé et de l’exécuter avec Azure Machine Learning sans avoir à modifier votre code.

Remarques :
- Le script d’entraînement fourni montre comment journaliser certaines métriques dans votre exécution Azure ML en utilisant l’objet `Run` dans le script.
- Le script d’entraînement fourni utilise les exemples de données de la fonction `iris = datasets.load_iris()`.  Pour vos propres données, vous devrez peut-être suivre des étapes telles que [Charger un jeu de données et des scripts](how-to-train-keras.md#data-upload) pour rendre les données disponibles pendant l’apprentissage.

### <a name="define-your-environment"></a>Définissez votre environnement.

#### <a name="create-a-custom-environment"></a>Créez un environnement personnalisé.

Créez votre environnement conda (sklearn-env.yml).
Pour écrire l’environnement conda à partir d’un notebook, vous pouvez ajouter la ligne ```%%writefile sklearn-env.yml``` en haut de la cellule.

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Créez un environnement Azure ML à partir de cette spécification de l’environnement conda. L’environnement sera empaqueté dans un conteneur Docker au moment de l’exécution.
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
```

#### <a name="use-a-curated-environment"></a>Utiliser un environnement organisé
Azure ML fournit des environnements de conteneur prédéfinis et organisés si vous ne souhaitez pas créer votre propre image. Pour plus d’informations, consultez [ceci](resource-curated-environments.md).
Si vous souhaitez utiliser un environnement organisé, vous pouvez exécuter la commande suivante à la place :

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>Créer un ScriptRunConfig

Ce ScriptRunConfig enverra votre tâche pour exécution sur la cible de calcul locale.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
src.run_config.environment = myenv
```

Si vous souhaitez envoyer des modifications à un cluster distant, vous pouvez remplacer run_config.target par la cible de calcul souhaitée.

### <a name="submit-your-run"></a>Envoyer votre exécution
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning exécute des scripts d’entraînement en copiant l’intégralité du répertoire source. Si vous avez des données sensibles que vous ne souhaitez pas charger, utilisez un [fichier .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou ne l’incluez pas dans le répertoire source. Au lieu de cela, accédez à vos données à l’aide d’un [magasin de données](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).

Pour plus d’informations sur la personnalisation de votre environnement Python, consultez [Créer et gérer des environnements pour l’entraînement et le déploiement](how-to-use-environments.md). 

## <a name="what-happens-during-run-execution"></a>Ce qui se passe lors de l’exécution
Quand l’exécution est lancée, elle passe par les phases suivantes :

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

Inscrivez le modèle sur votre espace de travail avec le code suivant. En spécifiant les paramètres `model_framework`, `model_framework_version` et `resource_configuration`, le modèle de déploiement sans code devient disponible. Le modèle de déploiement sans code vous permet de déployer directement votre modèle en tant que service web à partir du modèle inscrit, et l’objet [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) définit la ressource de calcul pour le service web.

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

Au lieu de la route de déploiement traditionnelle, vous pouvez également utiliser la fonctionnalité de déploiement sans code (préversion) pour scikit-learn. Le modèle de déploiement sans code est pris en charge pour tous les types de modèles scikit-learn intégrés. En inscrivant votre modèle comme indiqué ci-dessus avec les paramètres `model_framework`, `model_framework_version` et `resource_configuration`, vous pouvez simplement utiliser la fonction statique [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) pour déployer votre modèle.

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
