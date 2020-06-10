---
title: Exécuter des prédictions par lots sur le Big Data
titleSuffix: Azure Machine Learning
description: Découvrez comment obtenir des inférences de façon asynchrone sur de grandes quantités de données à l’aide de ParallelRunStep dans Azure Machine Learning. ParallelRunStep offre des fonctionnalités de traitement parallèle prêtes à l’emploi, et optimise l’inférence autonome (« fire-and-forget ») à débit élevé lors de l’utilisation de Big Data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 04/15/2020
ms.custom: Build2020, tracking-python
ms.openlocfilehash: b26527321cf7fc5ca7fc4b061f11b86f8830ec29
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84552315"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Exécuter l’inférence par lots sur de grandes quantités de données à l’aide d’Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment exécuter des inférences par lots sur de grandes quantités de données, de façon asynchrone et en parallèle avec Azure Machine Learning. ParallelRunStep fournit des capacités de parallélisme prêtes à l’emploi.

Avec ParallelRunStep, il est facile de mettre à l’échelle des inférences hors connexion vers de gros clusters de machines, sur des téraoctets de données structurées ou non structurées, en améliorant la productivité et en optimisant les coûts.

Dans cet article, vous apprenez à effectuer les tâches suivantes :

> * Configuration des ressources Machine Learning.
> * Configuration des entrées et de la sortie des données d’inférence par lots.
> * Préparation du modèle de classification d’image pré-entraîné et basé sur le jeu de données [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/). 
> * Écriture de votre script d’inférence.
> * Création d’un [pipeline Machine Learning](concept-ml-pipelines.md) contenant ParallelRunStep et exécution de l’inférence par lots sur les images de test MNIST. 
> * Nouvelle soumission d’une exécution d’inférence par lots avec de nouveaux paramètres et entrées de données. 

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

* Si vous n’avez pas encore d’espace de travail Azure Machine Learning, suivez le [tutoriel de configuration](tutorial-1st-experiment-sdk-setup.md) afin de bénéficier d’un démarrage rapide guidé. 

* Pour gérer votre environnement et vos dépendances, consultez le [guide pratique](how-to-configure-environment.md) sur la configuration de votre propre environnement local.

## <a name="set-up-machine-learning-resources"></a>Configurer des ressources Machine Learning

Les actions suivantes permettent de configurer les ressources Machine Learning dont vous avez besoin pour exécuter un pipeline d’inférence par lots :

- Connexion à un espace de travail.
- Création ou attache d’une ressource de calcul existante.

### <a name="configure-workspace"></a>Configurer l’espace de travail

Créez un objet d’espace de travail à partir de l’espace de travail existant. `Workspace.from_config()` lit le fichier config.json et charge les détails dans un objet nommé ws.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Cet extrait de code s’attend à ce que la configuration de l’espace de travail soit enregistrée dans le répertoire actif ou son parent. Pour plus d’informations sur la création d’un espace de travail, consultez [Créer et gérer les espaces de travail Azure Machine Learning](how-to-manage-workspace.md). Pour plus d’informations sur l’enregistrement de la configuration dans un fichier, consultez [Créer un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Créer une cible de calcul

Dans Azure Machine Learning, le *calcul* (ou la *cible de calcul*) fait référence aux machines ou aux clusters qui effectuent les étapes de calcul de votre pipeline Machine Learning. Exécutez le code suivant pour créer une cible [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) basée sur le processeur.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="configure-inputs-and-output"></a>Configurer les entrées et la sortie

### <a name="create-a-datastore-with-sample-images"></a>Créer un magasin de données avec des exemples d’images

Récupérez le jeu d’évaluation MNIST à partir du conteneur d’objets blob public `sampledata` dans le compte nommé `pipelinedata`. Créez un magasin de données portant le nom de `mnist_datastore` et pointant vers ce conteneur. Dans l’appel suivant à `register_azure_blob_container`, le fait de définir l’indicateur `overwrite` sur `True` remplace tous les magasins de données précédemment créés avec ce nom. 

Vous pouvez modifier cette étape pour pointer vers votre conteneur d’objets blob en fournissant vos propres valeurs pour `datastore_name`, `container_name` et `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Ensuite, spécifiez le magasin de données par défaut de l’espace de travail en tant que magasin de données de sortie. Vous l’utiliserez pour la sortie de l’inférence.

Lorsque vous créez votre espace de travail, des éléments de [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  et [Stockage Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)  sont joints à l’espace de travail par défaut. Azure Files représente le magasin de données par défaut pour un espace de travail, mais vous pouvez également utiliser le stockage d’objets Blob en tant que magasin de données. Pour plus d’informations, consultez [Options de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Créer les entrées de données

Les entrées de l’inférence par lots sont les données que vous souhaitez partitionner pour un traitement parallèle. Un pipeline d’inférence par lots accepte les entrées de données par l’intermédiaire de [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py).

`Dataset` sert à explorer, transformer et gérer les données dans Azure Machine Learning. Il existe deux types : [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) et [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). Dans cet exemple, vous utilisez `FileDataset` en tant qu’entrées. `FileDataset` vous offre la possibilité de télécharger ou de monter les fichiers dans votre calcul. En créant un jeu de données, vous créez une référence à l’emplacement de la source de données. Si vous avez appliqué des transformations au jeu de données par la création de sous-ensembles, celles-ci seront également stockées dans le jeu de données. Les données restant à leur emplacement existant, aucun coût de stockage supplémentaire n’est encouru.

Pour plus d’informations sur les jeux de données Azure Machine Learning, consultez [Créer des jeux de données et y accéder (préversion)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Afin de pouvoir utiliser des entrées de données dynamiques lors de l’exécution du pipeline d’inférence par lots, vous pouvez définir les entrées `Dataset` en tant que [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py). Vous pouvez spécifier le jeu de données des entrées chaque fois que vous soumettez de nouveau une exécution de pipeline d’inférence par lots.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Créer la sortie

Les objets [`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) sont utilisés pour transférer les données intermédiaires entre chaque étape du pipeline. Dans cet exemple, vous les utiliserez pour les sorties d’inférence.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

## <a name="prepare-the-model"></a>Préparer le modèle

[Téléchargez le modèle de classification des images pré-entraînées](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz), puis extrayez-le dans le répertoire `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Enregistrez ensuite le modèle avec votre espace de travail, afin qu’il soit disponible pour votre ressource de calcul.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Écrire votre script d’inférence

>[!Warning]
>Le code suivant n’est qu’un exemple que l’[exemple de notebook](https://aka.ms/batch-inference-notebooks) utilise. Vous devez créer votre propre script pour votre scénario.

Le script *doit contenir* deux fonctions :
- `init()`: utilisez cette fonction pour toute préparation coûteuse ou courante à une prochaine inférence. Par exemple, utilisez-la pour charger le modèle dans un objet global. Cette fonction est appelée une seule fois au début du processus.
-  `run(mini_batch)`: cette fonction s’exécute pour chaque instance de `mini_batch`.
    -  `mini_batch`: ParallelRunStep appellera la méthode d’exécution et passera soit une liste, soit un dataframe Pandas en tant qu’argument à la méthode. Chaque entrée de mini_batch sera un chemin de fichier si l’entrée est un FileDataset, ou un dataframe Pandas si l’entrée est un TabularDataset.
    -  `response`: la méthode run() doit retourner un dataframe Pandas ou un tableau. Pour append_row output_action, les éléments retournés sont ajoutés au fichier de sortie commun. Pour summary_only, le contenu des éléments est ignoré. Pour toutes les actions de sortie, chaque élément de sortie retourné indique la réussite de l’exécution d’une entrée dans le mini-lot d’entrée. Vous devez vérifier que suffisamment de données sont incluses dans le résultat de l’exécution pour mapper l’entrée au résultat de la sortie de l’exécution. La sortie de l’exécution sera écrite dans le fichier de sortie, mais pas nécessairement dans l’ordre. Vous devez utiliser une clé dans la sortie pour la mapper à l’entrée.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Si vous avez un autre fichier ou dossier dans le même répertoire que votre script d’inférence, vous pouvez le référencer en recherchant le répertoire de travail actuel.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Générer et exécuter le pipeline contenant ParallelRunStep

À présent, vous avez tout ce dont vous avez besoin : les entrées de données, le modèle, la sortie et votre script d’inférence. Créons le pipeline d’inférence par lots contenant ParallelRunStep.

### <a name="prepare-the-environment"></a>Préparer l’environnement

Vous devez d’abord spécifier les dépendances de votre script. Cela vous permet d’installer des packages Pip et de configurer l’environnement. Veillez à toujours inclure les packages **azureml-core** et **azureml-dataprep[pandas, fuse]** .

Si vous utilisez une image Docker personnalisée (user_managed_dependencies=True), Conda doit également être installé.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow",
                                                          "azureml-core", "azureml-dataprep[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Spécifier les paramètres à l’aide de ParallelRunConfig

`ParallelRunConfig` est la configuration principale de l’instance `ParallelRunStep` dans le pipeline Azure Machine Learning. Elle permet de wrapper votre script et de configurer les paramètres nécessaires, y compris les suivants :
- `entry_script`: script utilisateur utilisé comme un chemin de fichier local qui sera exécuté en parallèle sur plusieurs nœuds. Si `source_directory` est présent, utilisez un chemin relatif. Dans le cas contraire, utilisez un chemin accessible sur la machine.
- `mini_batch_size`: taille du mini-lot passé à un appel `run()` unique (Facultatif ; la valeur par défaut est `10` fichiers pour FileDataset et `1MB` pour TabularDataset.)
    - Pour `FileDataset`, il s’agit du nombre de fichiers avec une valeur minimale de `1`. Vous pouvez combiner plusieurs fichiers dans un mini-lot.
    - Pour `TabularDataset`, il s’agit de la taille des données. Par exemple, il peut s’agir des valeurs `1024`, `1024KB`, `10MB` ou `1GB`. `1MB` est la valeur recommandée. Le mini-lot de `TabularDataset` ne franchira jamais les limites du fichier. Par exemple, si vous avez des fichiers .csv de différentes tailles, le plus petit fichier aura une taille de 100 Ko et le plus grand une taille de 10 Mo. Si vous définissez `mini_batch_size = 1MB`, les fichiers dont la taille est inférieure à 1 Mo seront traités ensemble comme un mini-lot. Les fichiers dont la taille est supérieure à 1 Mo seront répartis dans plusieurs mini-lots.
- `error_threshold`: nombre d’échecs d’enregistrement pour `TabularDataset` et d’échecs de fichiers pour `FileDataset` qui doivent être ignorés pendant le traitement. Si le nombre d’erreurs présentes dans la totalité de l’entrée dépasse cette valeur, le travail est annulé. Le seuil d’erreur concerne la totalité de l’entrée et non le mini-lot envoyé à la méthode `run()`. La plage est la suivante : `[-1, int.max]`. La partie `-1` indique qu’il faut ignorer tous les échecs au cours du traitement.
- `output_action`: l’une des valeurs suivantes indique comment la sortie sera organisée :
    - `summary_only`: le script utilisateur va stocker la sortie. `ParallelRunStep` utilisera la sortie uniquement pour le calcul du seuil d’erreurs.
    - `append_row`: pour toutes les entrées, un seul fichier sera créé dans le dossier de sortie. Vous y ajouterez toutes les sorties séparées par une ligne.
- `append_row_file_name`: permet de personnaliser le nom du fichier de sortie pour append_row output_action (facultatif ; la valeur par défaut est `parallel_run_step.txt`).
- `source_directory`: chemins des dossiers qui contiennent tous les fichiers à exécuter sur la cible de calcul (facultatif).
- `compute_target`: Seul `AmlCompute` est pris en charge.
- `node_count`: nombre de nœuds de calcul à utiliser pour l’exécution du script utilisateur.
- `process_count_per_node`: nombre de processus par nœud. La bonne pratique consiste à définir la valeur sur le nombre de GPU ou d’UC dont dispose un nœud (facultatif ; la valeur par défaut est `1`).
- `environment`: définition de l’environnement Python. Vous pouvez la configurer de manière à utiliser un environnement Python existant ou un environnement temporaire. La définition est également chargée de définir les dépendances d’application nécessaires (facultatif).
- `logging_level`: Verbosité du journal. Les valeurs permettant d’augmenter le niveau de verbosité sont les suivantes : `WARNING`, `INFO` et `DEBUG`. (Facultatif ; la valeur par défaut est `INFO`.)
- `run_invocation_timeout`: délai d’attente de l’appel de la méthode `run()`, en secondes. (Facultatif ; la valeur par défaut est `60`.)
- `run_max_try`: nombre maximal de tentatives de `run()` pour un mini-lot. `run()` a échoué si une exception est levée, ou si rien n’est retourné lorsque `run_invocation_timeout` est atteint (facultatif ; la valeur par défaut est `3`). 

Vous pouvez spécifier `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` et `run_max_try` en tant que `PipelineParameter` ; ainsi, lorsque vous soumettez à nouveau une exécution de pipeline, vous pouvez ajuster les valeurs des paramètres. Dans cet exemple, vous utilisez PipelineParameter pour `mini_batch_size` et `Process_count_per_node`, et vous modifierez ces valeurs quand vous soumettrez à nouveau une exécution ultérieurement. 

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Créer ParallelRunStep

Créez ParallelRunStep à l’aide du script, de la configuration de l’environnement et des paramètres. Spécifiez la cible de calcul que vous avez déjà jointe à votre espace de travail en tant que cible d’exécution de votre script d’inférence. Utilisez `ParallelRunStep` pour créer l’étape de pipeline d’inférence par lots, qui accepte tous les paramètres suivants :
- `name`: nom de l’étape, avec les restrictions de nommage suivantes : unique, de 3 à 32 caractères et expression régulière ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: objet `ParallelRunConfig`, comme défini précédemment.
- `inputs`: un ou plusieurs jeux de données Azure Machine Learning à un seul type, à partitionner pour un traitement parallèle.
- `side_inputs`: une ou plusieurs données, ou jeux de données de référence utilisés comme entrées supplémentaires sans avoir besoin d’être partitionnés.
- `output`: objet `PipelineData` qui correspond au répertoire de sortie.
- `arguments`: liste d’arguments passés au script utilisateur. Utilisez unknown_args pour les récupérer dans votre script d’entrée (facultatif).
- `allow_reuse`: indique si l’étape doit réutiliser les résultats précédents lorsqu’elle est exécutée avec les mêmes paramètres ou entrées. Si ce paramètre a la valeur `False`, une nouvelle exécution sera toujours générée pour cette étape pendant l’exécution du pipeline (Facultatif ; la valeur par défaut est `True`.)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>Créer et exécuter le pipeline

À présent, exécutez le pipeline. Commencez par créer un objet [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) en utilisant votre référence d’espace de travail et l’étape de pipeline que vous avez créée. Le paramètre `steps` est un tableau d’étapes. Dans le cas présent, l’inférence par lots ne comporte qu’une seule étape. Pour créer des pipelines qui comportent plusieurs étapes, placez ces dernières dans l’ordre dans ce tableau.

Utilisez ensuite la fonction `Experiment.submit()` pour soumettre l’exécution du pipeline.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Superviser le travail d’inférence par lots

La durée d’un travail d’inférence par lots peut être très longue. Cet exemple supervise la progression à l’aide d’un widget Jupyter. Vous pouvez également superviser la progression du travail en utilisant :

* Azure Machine Learning Studio 
* La sortie de console de l’objet [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py).

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Soumettre à nouveau une exécution avec de nouveaux paramètres et entrées de données

Dès lors que vous avez constitué les entrées et plusieurs configurations sous la forme de `PipelineParameter`, vous pouvez soumettre à nouveau une exécution de l’inférence par lots avec une entrée de jeu de données différente, et ajuster les paramètres sans avoir à créer entièrement un nouveau pipeline. Vous allez vous servir du même magasin de données, mais n’utiliser qu’une seule image comme entrées de données.

```python
path_on_datastore = mnist_data.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
single_image_ds._ensure_saved(ws)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Étapes suivantes

Pour voir le déroulement complet de ce processus, essayez le [notebook d’inférence par lots](https://aka.ms/batch-inference-notebooks). 

Pour obtenir des conseils de débogage et de dépannage de ParallelRunStep, consultez le [guide pratique](how-to-debug-parallel-run-step.md).

Pour obtenir des conseils sur le débogage et le dépannage des pipelines, consultez le [guide pratique](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

