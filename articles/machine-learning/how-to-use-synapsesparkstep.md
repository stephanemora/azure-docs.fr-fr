---
title: Utiliser Apache Spark dans un pipeline de Machine Learning (préversion)
titleSuffix: Azure Machine Learning
description: Liez votre espace de travail Azure Synapse Analytics à votre pipeline Azure Machine Learning afin d’utiliser Apache Spark pour la manipulation des données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576378"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Guide pratique pour utiliser Apache Spark (alimenté par Azure Synapse Analytics) dans votre pipeline Machine Learning (préversion)

Dans cet article, vous allez apprendre à utiliser des pools Apache Spark alimentés par Azure Synapse Analytics comme cible de calcul pour une étape de préparation des données dans un pipeline Azure Machine Learning. Vous découvrirez comment un même pipeline peut utiliser des ressources de calcul adaptées à l’étape spécifique, telle que la préparation des données ou l’entraînement. Vous verrez comment les données sont préparées pour l’étape Spark et comment elles sont passées à l’étape suivante. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Créez un [espace de travail Azure Machine Learning](how-to-manage-workspace.md) afin de contenir toutes les ressources de votre pipeline.

* [Configurez votre environnement de développement](how-to-configure-environment.md) pour installer le SDK Azure Machine Learning ou utilisez une [instance de calcul Azure Machine Learning](concept-compute-instance.md) avec le SDK déjà installé.

* Créez un espace de travail Azure Synapse Analytics et un pool Apache Spark (voir [Démarrage rapide : Créer un pool Apache Spark serverless avec Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Lier votre espace de travail Azure Machine Learning et l’espace de travail Azure Synapse Analytics 

Vous créez et administrez vos pools Apache Spark dans un espace de travail Azure Synapse Analytics. Pour intégrer un pool Apache Spark à un espace de travail Azure Machine Learning, vous devez créer une [liaison vers l’espace de travail Azure Synapse Analytics](how-to-link-synapse-ml-workspaces.md). 

Vous pouvez attacher un pool Apache Spark par le biais de l’interface utilisateur d’Azure Machine Learning Studio à l’aide de la page **Services liés**. Vous pouvez également le faire par le biais de la page **Calcul** avec l’option **Joindre un calcul**.

Vous pouvez également attacher un pool Apache Spark par le biais d’un SDK (comme indiqué ci-dessous) ou d’un modèle ARM (voir cet [exemple de modèle ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

Vous pouvez utiliser la ligne de commande pour suivre le modèle ARM, ajouter le service lié et attacher le pool Apache Spark à l’aide du code suivant :

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Pour établir correctement une liaison à l’espace de travail Azure Synapse Analytics, vous devez disposer du rôle Propriétaire dans la ressource de l’espace de travail Azure Synapse Analytics. Vérifiez votre accès dans le portail Azure.
> Le service lié obtient une identité attribuée par le système quand vous le créez. Vous devez attribuer à cette identité du service de liaison le rôle « Administrateur Synapse Apache Spark » de Synapse Studio afin qu’elle puisse envoyer le travail Spark (voir [Gestion des attributions de rôles RBAC Synapse dans Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Vous devez également attribuer à l’utilisateur de l’espace de travail Azure Machine Learning le rôle « Contributeur » à partir du portail Azure de la gestion des ressources.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Créer ou récupérer le lien entre votre espace de travail Azure Synapse Analytics et votre espace de travail Azure Machine Learning

Vous pouvez récupérer des services liés dans votre espace de travail à l’aide d’un code tel que celui-ci :

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Tout d’abord, `Workspace.from_config()` accède à votre espace de travail Azure Machine Learning à l’aide de la configuration définie dans `config.json` (voir [Didacticiel : Bien démarrer avec Azure Machine Learning dans votre environnement de développement](tutorial-1st-experiment-sdk-setup-local.md)). Ensuite, le code affiche tous les services liés disponibles dans l’espace de travail. Enfin, `LinkedService.get()` récupère un service lié nommé `'synapselink1'`. 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Attacher votre pool Apache Spark en tant que cible de calcul pour Azure Machine Learning

Pour utiliser votre pool Apache Spark afin d’alimenter une étape de votre pipeline Machine Learning, vous devez l’attacher en tant que `ComputeTarget` pour l’étape de pipeline, comme illustré dans le code suivant.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

La première étape consiste à configurer le `SynapseCompute`. L’argument `linked_service` est l’objet `LinkedService` que vous avez créé ou récupéré à l’étape précédente. L’argument `type` doit être `SynapseSpark`. L’argument `pool_name` dans `SynapseCompute.attach_configuration()` doit correspondre à celui d’un pool existant dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations sur la création d’un pool Apache Spark dans l’espace de travail Azure Synapse Analytics, consultez [Démarrage rapide : Créer un pool Apache Spark serverless avec Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Le type de `attach_config` est `ComputeTargetAttachConfiguration`.

Une fois la configuration créée, vous créez une `ComputeTarget` de Machine Learning en passant le `Workspace`, la `ComputeTargetAttachConfiguration` et le nom par lequel vous souhaitez faire référence au calcul dans l’espace de travail de Machine Learning. L’appel à `ComputeTarget.attach()` étant asynchrone, l’exemple se bloque jusqu’à la fin de l’appel.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Créer une `SynapseSparkStep` qui utilise le pool Apache Spark lié

L’exemple de notebook [Travail Spark sur le pool Synapse Spark](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) définit un pipeline de Machine Learning simple. Tout d’abord, le notebook définit une étape de préparation des données gérée par le `synapse_compute` défini à l’étape précédente. Ensuite, le notebook définit une étape d’entraînement gérée par une cible de calcul mieux adaptée à l’entraînement. L’exemple de notebook utilise la base de données Titanic Survival pour démontrer l’entrée et la sortie de données ; il ne nettoie pas les données et ne crée pas de modèle prédictif à proprement parler. Étant donné qu’il n’y a pas d’entraînement réel dans cet exemple, l’étape d’entraînement utilise une ressource de calcul basée sur le processeur peu coûteuse.

Les données circulent dans un pipeline de Machine Learning par le biais d’objets `DatasetConsumptionConfig`, qui peuvent contenir des données tabulaires ou des ensembles de fichiers. Les données proviennent souvent de fichiers d’un stockage d’objets blob situé dans le magasin de données d’un espace de travail. Le code suivant illustre un code standard permettant de créer une entrée pour un pipeline de Machine Learning :

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

Le code ci-dessus part du principe que le fichier `Titanic.csv` se trouve dans un stockage d’objets blob. Le code montre comment lire le fichier en tant que `TabularDataset` et en tant que `FileDataset`. Ce code est fourni à des fins de démonstration uniquement, car il serait déroutant de dupliquer des entrées ou d’interpréter une source de données unique à la fois comme une ressource contenant une table et comme simplement un fichier.

> [!Important]
> Pour que vous puissiez utiliser `FileDataset` comme entrée, la version d’`azureml-core` doit être au moins égale à `1.20.0`. La spécification de cette configuration à l’aide de la classe `Environment` est décrite ci-après.

Quand une étape est terminée, vous pouvez choisir de stocker les données de sortie à l’aide d’un code similaire à ce qui suit :

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

En l’occurrence, les données sont stockées dans le `datastore` dans un fichier appelé `test` et sont disponibles dans l’espace de travail de Machine Learning en tant que `Dataset` avec le nom `registered_dataset`.

En plus des données, une étape de pipeline peut avoir des dépendances Python par étape. Les objets `SynapseSparkStep` individuels peuvent également spécifier leur configuration Azure Synapse Apache Spark précise. Cela est illustré dans le code suivant, qui spécifie que la version du package `azureml-core` doit être au moins égale à `1.20.0`. (Comme mentionné plus haut, cette exigence pour `azureml-core` est nécessaire pour utiliser un `FileDataset` comme entrée.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

Le code ci-dessus spécifie une étape unique dans le pipeline de Machine Learning Azure. L’`environment` de cette étape spécifie une version `azureml-core` spécifique et peut ajouter d’autres dépendances Conda ou pip si nécessaire.

Le `SynapseSparkStep` compresse et charge le sous-répertoire `./code` à partir de l’ordinateur local. Ce répertoire est recréé sur le serveur de calcul et l’étape exécute le fichier `dataprep.py` à partir de ce répertoire. Les `inputs` et `outputs` de cette étape sont les objets `step1_input1`, `step1_input2` et `step1_output` abordés plus haut. Le moyen le plus simple d’accéder à ces valeurs dans le script `dataprep.py` consiste à les associer à des `arguments` nommés.

L’ensemble d’arguments suivant pour le constructeur `SynapseSparkStep` contrôle Apache Spark. `compute_target` est le `'link1-spark01'` que nous avons joint en tant que cible de calcul. Les autres paramètres spécifient la mémoire et les cœurs que nous souhaitons utiliser.

L’exemple de notebook utilise le code suivant pour `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Ce script de préparation des données n’effectue aucune transformation de données réelle, mais il montre comment récupérer des données, les convertir en dataframe Spark et effectuer certaines manipulations Apache Spark de base. Vous pouvez trouver la sortie dans Azure Machine Learning Studio en ouvrant l’exécution enfant, en choisissant l’onglet **Sorties + journaux** et en ouvrant le fichier `logs/azureml/driver/stdout`, comme indiqué dans l’illustration suivante.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Capture d’écran de Studio montrant l’onglet stdout de l’exécution enfant":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Utiliser le `SynapseSparkStep` dans un pipeline

L’exemple suivant utilise la sortie de `SynapseSparkStep` créée dans la [section précédente](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool). D’autres étapes dans le pipeline peuvent avoir leurs propres environnements et s’exécuter sur différentes ressources de calcul appropriées à la tâche en cours. L’exemple de notebook exécute « l’étape d’entraînement » sur un petit cluster de processeurs :

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

Le code ci-dessus crée la ressource de calcul si nécessaire. Ensuite, le résultat `step1_output` est converti en entrée pour l’étape d’entraînement. L’option `as_download()` signifie que les données sont déplacées vers la ressource de calcul, ce qui permet un accès plus rapide. Si les données sont trop volumineuses pour ne pas contenir sur le disque dur de calcul local, vous pouvez utiliser l’option `as_mount()` afin de diffuser les données en streaming via le système de fichiers FUSE. La `compute_target` de cette deuxième étape est `'cpucluster'`, et non la ressource `'link1-spark01'` que vous avez utilisée lors de l’étape de préparation des données. Cette étape utilise un programme simple `train.py` à la place de `dataprep.py` que vous avez utilisé à l’étape précédente. Vous pouvez voir les détails de `train.py` dans l’exemple de notebook.

Après avoir défini toutes vos étapes, vous pouvez créer et exécuter votre pipeline. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Le code ci-dessus crée un pipeline constitué de l’étape de préparation des données sur des pools Apache Spark gérés par Azure Synapse Analytics (`step_1`) et de l’étape d’entraînement (`step_2`). Azure calcule le graphique d’exécution en examinant les dépendances de données entre les étapes. En l’occurrence, il n’existe qu’une dépendance simple selon laquelle `step2_input` nécessite obligatoirement `step1_output`.

L’appel à `pipeline.submit` crée, si nécessaire, une expérience appelée `synapse-pipeline` et y commence une exécution de manière asynchrone. Les étapes individuelles au sein du pipeline sont exécutées en tant qu’exécutions enfants de cette exécution principale et peuvent être supervisées et consultées dans la page Expériences de Studio.

## <a name="next-steps"></a>Étapes suivantes

* [Publier et suivre des pipelines Machine Learning](how-to-deploy-pipelines.md) 
* [Superviser Azure Machine Learning](monitor-azure-machine-learning.md)
* [Utiliser le ML automatisé dans un pipeline Azure Machine Learning dans Python](how-to-use-automlstep-in-pipelines.md)
