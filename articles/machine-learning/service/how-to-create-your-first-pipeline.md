---
title: Créer, exécuter et effectuer le suivi des pipelines ML
titleSuffix: Azure Machine Learning
description: Créez et exécutez un pipeline Machine Learning avec le SDK Azure Machine Learning pour Python. Utilisez des pipelines ML pour créer et gérer les flux de travail qui combinent les phases de Machine Learning (ML). Ces phases incluent la préparation des données, l’apprentissage du modèle, le déploiement du modèle et l’inférence/le scoring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 85c80a5b9f2c0ac7b73fb51fd8138e3aae0b0221
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894668"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Créer et exécuter des pipelines de Machine Learning avec le kit SDK Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous apprendrez à créer, publier et exécuter un [pipeline Machine Learning](concept-ml-pipelines.md) et en effectuer le suivi à l’aide du [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Utilisez des **pipeline ML** pour créer un workflow qui combine plusieurs phases ML, puis publiez ce pipeline dans votre espace de travail Azure Machine Learning pour y accéder ultérieurement ou le partager avec d’autres.  Les pipelines ML sont idéaux pour les scénarios de scoring par lots, car ils utilisent différents calculs, réutilisent les étapes au lieu de les réexécuter et partagent les flux de travail ML avec d’autres.

Vous pouvez utiliser un autre type de pipeline appelé [pipeline Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) pour l’automatisation CI/CD des tâches de ML, mais ce type de pipeline n’est jamais stocké dans votre espace de travail. [Comparez ces différents pipelines](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Chaque phase d’un pipeline ML, telle que la préparation des données et l’entraînement du modèle, peut inclure une ou plusieurs étapes.

Les pipelines ML que vous créez sont visibles par les membres de votre [espace de travail](how-to-manage-workspace.md) d’Azure Machine Learning. 

Les pipelines ML utilisent des cibles de calcul à distance pour le calcul et le stockage des données intermédiaires et finales associées à ce pipeline. Ils peuvent lire et écrire des données à partir/vers les emplacements de [Stockage Azure](https://docs.microsoft.com/azure/storage/) pris en charge.

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prérequis

* Créez un [espace de travail Azure Machine Learning](how-to-manage-workspace.md) afin de contenir toutes les ressources de votre pipeline.

* [Configurez votre environnement de développement](how-to-configure-environment.md) pour installer le SDK Azure Machine Learning ou utilisez une [machine virtuelle de notebooks Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) avec le SDK déjà installé.

Commencez par attacher votre espace de travail :

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Configurer des ressources Machine Learning

Créez les ressources requises pour exécuter un pipeline ML :

* Configurez un magasin de données permettant d’accéder aux données requises pour les étapes du pipeline.

* Configurez un objet `DataReference` qui pointe vers les données qui se trouvent dans un magasin de données ou qui sont accessibles à partir de ce biais.

* Configurez les [cibles de calcul](concept-azure-machine-learning-architecture.md#compute-targets) sur lesquelles vous souhaitez que les étapes du pipeline s’exécutent.

### <a name="set-up-a-datastore"></a>Configurer un magasin de données

Le magasin de données stocke les données accessibles par le pipeline. Chaque espace de travail dispose d’un magasin de données par défaut. Vous pouvez inscrire des magasins de données supplémentaires. 

Lorsque vous créez votre espace de travail, des éléments [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) et de [stockage d’objets Blob Azure ](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) sont joints à l’espace de travail. Une banque de données par défaut est inscrite pour se connecter au stockage d’objets blob Azure. Pour en savoir plus , voir [Quand utiliser des fichiers Azure, des objets blob Azure, des fichiers Azure ou des disques Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Téléchargez les fichiers de données ou les répertoires sur le magasin de données afin que vos pipelines puissent y accéder. Cet exemple utilise le stockage d’objets blob comme magasin de données :

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Un pipeline est constitué d’une ou plusieurs étapes. Une étape représente une unité d’exécution sur une cible de calcul. Il se peut que les étapes consomment les sources de données et produisent des données « intermédiaires ». Une étape peut créer des données telles qu’un modèle, un répertoire avec un modèle et des fichiers dépendants ou des données temporaires. Ces données sont ensuite mises à disposition des autres étapes du pipeline.

### <a name="configure-data-reference"></a>Configurer la référence de données

Vous venez de créer une source de données susceptible d’être référencée dans un pipeline en tant qu’entrée ou étape. Dans un pipeline, une source de données est représentée par un objet [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference). L’objet `DataReference` pointe vers les données qui se trouvent dans un magasin de données ou qui sont accessibles par ce biais.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Les données intermédiaires (ou la sortie d’une étape) sont représentées par un objet [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` est généré en tant que sortie d’une étape et utilisé en tant qu’entrée pour une ou plusieurs étapes ultérieures. `PipelineData` présente une dépendance de données entre les étapes et crée un ordre d’exécution implicite dans le pipeline. Cet objet sera utilisé ultérieurement lors de la création d’étapes de pipeline.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Configurer des données à l’aide de jeux de données

Si vous disposez de données tabulaires stockées dans un fichier ou un ensemble de fichiers, un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) constitue une alternative efficace à une `DataReference`. Les objets `TabularDataset` prennent en charge le contrôle de version, les différences et les statistiques de résumé. Les `TabularDataset` sont évalués tardivement (comme les générateurs Python) et sont efficaces pour les scinder par fractionnement ou filtrage. La classe `FileDataset` fournit des données similaires évaluées tardivement qui représentent un ou plusieurs fichiers. 

Vous créez un `TabularDataset` à l’aide de méthodes telles que [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 Vous créez un `FileDataset` à l’aide de [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

 Pour plus d’informations sur l’utilisation des jeux de données, consultez [Ajouter et inscrire des jeux de données](how-to-create-register-datasets.md) ou [cet exemple de notebook](https://aka.ms/tabulardataset-samplenotebook).

## <a name="set-up-compute-target"></a>Configurer la cible de calcul

Dans Azure Machine Learning, le terme __calcul__ (ou __cible de calcul__) fait référence aux machines ou aux clusters qui effectuent les étapes de calculs de votre pipeline Machine Learning.   Consultez [Cibles de calcul pour effectuer l’entraînement des modèles](how-to-set-up-training-targets.md) pour obtenir la liste complète des cibles de calcul et savoir comment les créer et les joindre à votre espace de travail.  Le processus de création et/ou d’attachement d’une cible de calcul est le même, qu’il s’agisse d’effectuer l’apprentissage d’un modèle ou d’exécuter une étape de pipeline. Une fois que vous avez créé et joint votre cible de calcul, utilisez l’objet `ComputeTarget` dans votre [étape de pipeline](#steps).

> [!IMPORTANT]
> L’exécution d’opérations de gestion sur les cibles de calcul n’est pas prise en charge au sein des travaux distants. Les pipelines de Machine Learning étant envoyés sous forme de travail distant, n’effectuez pas d’opérations de gestion sur les cibles de calcul au sein du pipeline.

Vous trouverez ci-dessous des exemples de création et d’attachement de cibles de calcul pour :

* Capacité de calcul Azure Machine Learning
* Azure Databricks 
* Service Analytique Azure Data Lake

### <a name="azure-machine-learning-compute"></a>Capacité de calcul Azure Machine Learning

Vous pouvez créer une capacité de calcul Azure Machine Learning pour exécuter vos étapes.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks est un environnement basé sur Apache Spark dans le cloud Azure. Il peut être utilisé comme cible de calcul avec un pipeline Azure Machine Learning.

Créez un espace de travail Azure Databricks avant de l’utiliser. Pour créer une ressource d’espace de travail, consultez le document [Exécuter un travail Spark sur Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Pour joindre Azure Databricks comme cible de calcul, fournissez les informations suivantes :

* __Nom de la capacité de calcul Databricks__ : nom que vous voulez affecter à cette ressource de calcul.
* __Nom de l’espace de travail Databricks__ : nom de l’espace de travail Azure Databricks.
* __Jeton d’accès Databricks__ : jeton d’accès utilisé pour s’authentifier auprès d’Azure Databricks. Pour générer un jeton d’accès, consultez le document [Authentification](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

Le code suivant montre comment joindre Azure Databricks en tant que cible de calcul avec le kit de développement logiciel (SDK) Azure Machine Learning (__l’espace de travail Databricks doit être présent dans le même abonnement que votre espace de travail AML__) :

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Si vous souhaitez obtenir un exemple plus détaillé, veuillez consulter un [exemple de notebook](https://aka.ms/pl-databricks) sur GitHub.

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics est une plateforme analytique de Big Data dans le cloud Azure. Il peut être utilisé comme cible de calcul avec un pipeline Azure Machine Learning.

Créez un compte Azure Data Lake Analytics avant de l’utiliser. Pour créer cette ressource, consultez le document [Prise en main d’Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Pour attacher Data Lake Analytics comme cible de calcul, vous devez utiliser le Kit de développement logiciel Azure Machine Learning et fournir les informations suivantes :

* __Nom de la capacité de calcul__ : nom que vous voulez affecter à cette ressource de calcul.
* __Groupe de ressources__ : groupe de ressources contenant le compte Data Lake Analytics.
* __Nom du compte__ : Le nom du compte Data Lake Analytics.

Le code suivant montre comment attacher Data Lake Analytics comme cible de calcul :

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Si vous souhaitez obtenir un exemple plus détaillé, veuillez consulter un [exemple de notebook](https://aka.ms/pl-adla) sur GitHub.

> [!TIP]
> Les pipelines Azure Machine Learning peuvent uniquement fonctionner avec les données stockées dans le magasin de données par défaut du compte Data Lake Analytics. Si les données dont vous avez besoin se trouvent dans un magasin non défini par défaut, vous pouvez utiliser un [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) pour copier les données avant l’apprentissage.

## <a id="steps"></a>Composer les étapes de votre pipeline

Une fois que vous avez créé et joint une cible de calcul à votre espace de travail, vous êtes prêt à définir une étape de pipeline. De nombreuses étapes intégrées sont disponibles via le SDK Azure Machine Learning. [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) est la plus simple de ces étapes qui exécute un script Python dans une cible de calcul spécifiée :

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

La réutilisation des résultats précédents (`allow_reuse`) est essentielle lors de l’utilisation des pipelines dans un environnement de collaboration. En effet, cela permet de supprimer les réexécutions inutiles et ainsi d’offrir une grande souplesse. La réutilisation est le comportement par défaut quand le nom du script, les entrées et les paramètres d’une étape restent les mêmes. Lorsque la sortie de l’étape est réutilisée, le travail n’est pas envoyé pour le calcul. À la place, les résultats de l’exécution précédente sont immédiatement disponibles pour l’exécution de l’étape suivante. Si `allow_reuse` a la valeur false, une nouvelle exécution sera toujours générée pour cette étape pendant l’exécution du pipeline. 

Après avoir défini vos étapes, vous générez le pipeline à l’aide de tout ou partie de ces étapes.

> [!NOTE]
> Aucune donnée ni fichier n’est téléchargé sur Azure Machine Learning lorsque vous définissez les étapes ou générez le pipeline.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

L’exemple suivant utilise la cible de calcul Azure Databricks créée plus haut : 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Utiliser un jeu de données 

Pour utiliser un `TabularDataset` ou un `FileDataset` dans votre pipeline, vous devez le transformer en [DatasetConsumptionConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) en appelant [as_named_input(nom)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-). Vous transmettez cet objet `DatasetConsumptionConfig` en tant que l’un des `inputs` à votre étape de pipeline. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Vous récupérez ensuite le jeu de données dans votre pipeline à l’aide du dictionnaire [Run.input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets).

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Pour plus d’informations, consultez [azureml-pipeline-steps package](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) (package azureml-pipeline-step) et la référence [Pipeline class](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) (Classe Pipeline).

## <a name="submit-the-pipeline"></a>Envoyer le pipeline

Lorsque vous envoyez le pipeline, Azure Machine Learning vérifie les dépendances pour chaque étape et charge un instantané du répertoire source que vous avez spécifié. Si aucun répertoire source n’est spécifié, le répertoire local actuel est chargé. La capture instantanée est également stockée dans le cadre de l’expérience dans votre espace de travail.

> [!IMPORTANT]
> Pour empêcher les fichiers d’être inclus dans la capture instantanée, créez un élément [.gitignore](https://git-scm.com/docs/gitignore) ou un fichier `.amlignore` dans le répertoire et ajoutez-y les fichiers. Le fichier `.amlignore` utilise les mêmes modèles et syntaxe que le fichier [.gitignore](https://git-scm.com/docs/gitignore). Si les deux fichiers existent, le fichier `.amlignore` est prioritaire.
>
> Pour plus d’informations, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Lorsque vous exécutez un pipeline pour la première fois, Azure Machine Learning :

* Télécharge l’instantané du projet sur la cible de calcul à partir du stockage d’objets Blob associé à l’espace de travail.
* Génère une image docker correspondant à chaque étape du pipeline.
* Télécharge l’image Docker de chaque étape sur la cible de calcul à partir du registre de conteneurs.
* Monte la banque de données si un objet `DataReference` est spécifié dans une étape. Si le montage n’est pas pris en charge, alors les données sont copiées sur la cible de calcul.
* Exécute l’étape dans la cible de calcul spécifiée dans la définition de l’étape. 
* Crée les artefacts tels que les journaux d’activité, stdout et stderr,les métriques mesures et la sortie spécifiée par l’étape. Ces artefacts sont ensuite téléchargés et conservés dans le magasin de données par défaut de l’utilisateur.

![Diagramme d’exécution d’une expérience en tant que pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Pour plus d’informations, consultez la référence [Experiment class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) (Classe Experiment).

### <a name="view-results-of-a-pipeline"></a>Afficher les résultats d’un pipeline

Consultez la liste de tous vos pipelines et les détails relatifs à leur exécution dans le studio :

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com).

1. [Affichez votre espace de travail](how-to-manage-workspace.md#view).

1. Sur la gauche, sélectionnez **Pipelines** pour voir toutes vos exécutions de pipeline.
 ![Liste de pipelines Machine Learning](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Sélectionnez un pipeline spécifique pour afficher les résultats de l’exécution.

## <a name="git-tracking-and-integration"></a>Intégration et suivi Git

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Pour plus d’informations, consultez [Obtenir une intégration pour Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publier un pipeline

Vous pouvez publier un pipeline afin de l’exécuter ultérieurement avec des entrées différentes. Pour que le point de terminaison REST d’un pipeline déjà publié accepte les paramètres, vous devez paramétrer le pipeline avant sa publication.

1. Pour créer un paramètre de pipeline, utilisez un objet [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) avec une valeur par défaut.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Ajoutez cet objet `PipelineParameter` en tant que paramètre de l’une des étapes du pipeline comme suit :

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
     source_directory=project_folder)
   ```

3. Publiez ce pipeline. Il acceptera un paramètre en cas d’appel.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Exécuter un pipeline publié

Tous les pipelines publiés disposent d’un point de terminaison REST. Celui-ci appelle l’exécution du pipeline à partir de systèmes externes tels que les clients non-Python. Ce point de terminaison active la « répétabilité managée » dans les scénarios de scoring et de nouvel apprentissage.

Pour appeler l’exécution du pipeline précédent, vous devez disposer d’un jeton d’en-tête d’authentification Azure Active Directory, comme décrit dans la référence de la [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py). Pour plus de détails, consultez [Authentification dans Azure Machine Learning](https://aka.ms/pl-restep-auth).

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Créer un point de terminaison de pipeline en versions gérées
Vous pouvez créer un point de terminaison de pipeline avec plusieurs pipelines publiés derrière. Cela peut être utilisé comme un pipeline publié mais vous procure un point de terminaison REST au moment de l’itération et de la mise à jour de vos pipelines de Machine Learning.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Soumettre un travail à un point de terminaison de pipeline
Vous pouvez soumettre un travail à la version par défaut d’un point de terminaison de pipeline :
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Vous pouvez également envoyer un travail à une version spécifique :
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

La restauration peut être effectuée à l’aide de l’API REST :
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Utiliser des pipelines publiés dans le studio

Vous pouvez également exécuter un pipeline publié à partir du studio :

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com).

1. [Affichez votre espace de travail](how-to-manage-workspace.md#view).

1. Sur la gauche, sélectionnez **Points de terminaison**.

1. En haut, sélectionnez **Points de terminaison de pipeline**.
 ![Liste de pipelines publiés Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Sélectionnez un pipeline spécifique pour exécuter, utiliser ou examiner les résultats des exécutions précédentes du point de terminaison du pipeline.


### <a name="disable-a-published-pipeline"></a>Désactiver un pipeline publié

Pour masquer un pipeline de votre liste de pipelines publiés, vous le désactivez soit dans le studio, soit à partir du SDK :

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Vous pouvez le réactiver avec `p.enable()`. Pour plus d’informations, consultez la référence de la [classe PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py).


## <a name="caching--reuse"></a>Mise en cache et réutilisation  

Afin d’optimiser et de personnaliser le comportement de vos pipelines, vous pouvez effectuer quelques opérations autour de la mise en cache et de la réutilisation. Par exemple, vous pouvez choisir :
+ De **désactiver la réutilisation par défaut de la sortie d’exécution de l’étape** en définissant `allow_reuse=False` pendant la [définition de l’étape](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). La réutilisation est essentielle lors de l’utilisation des pipelines dans un environnement de collaboration. En effet, cela permet de supprimer les exécutions inutiles et ainsi d’offrir une grande souplesse. Toutefois, vous pouvez refuser la réutilisation.
+ De **forcer la régénération de sortie pour toutes les étapes dans une exécution** avec `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Par défaut, `allow_reuse` pour les étapes est activé et le `source_directory` spécifié dans la définition de l’étape est haché. Par conséquent, si le script pour une étape donnée reste le même (`script_name`, entrées et paramètres) et que rien d’autre n’a changé dans le ` source_directory`, la sortie d’une exécution d’étape précédente est réutilisée, le travail n’est pas envoyé pour le calcul et les résultats de l’exécution précédente sont immédiatement disponibles pour l’étape suivante à la place.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Étapes suivantes

- Utilisez [ces blocs-notes Jupyter sur GitHub](https://aka.ms/aml-pipeline-readme) pour explorer plus en détail les pipelines Machine Learning.
- Consultez l’aide relative à la référence SDK des packages [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).
- Consultez le [guide pratique](how-to-debug-pipelines.md) pour obtenir des conseils sur le débogage et la résolution des problèmes de pipelines.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
