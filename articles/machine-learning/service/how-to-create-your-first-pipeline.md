---
title: Créer, exécuter et effectuer le suivi des pipelines ML
titleSuffix: Azure Machine Learning service
description: Créez et exécutez un pipeline Machine Learning avec le SDK Azure Machine Learning pour Python. Vous utilisez des pipelines pour créer et gérer les flux de travail qui combinent les phases de Machine Learning (ML). Ces phases incluent la préparation des données, l’apprentissage du modèle, le modèle de déploiement et l’inférence.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6c6472b824eefdd1954f3645c69090d1fb5455de
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754456"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Créer et exécuter un pipeline Machine Learning à l’aide du SDK Azure Machine Learning

Dans cet article, vous apprendrez à créer, publier et exécuter un [pipeline Machine Learning](concept-ml-pipelines.md) et en effectuer le suivi à l’aide du [SDK Azure Machine Learning](https://aka.ms/aml-sdk).  Ces pipelines permettent de créer et de gérer des workflows qui combinent les différentes phases de Machine Learning. Chaque étape d’un pipeline, comme la préparation des données et l’apprentissage du modèle, peut inclure une ou plusieurs étapes.

Les pipelines que vous créez sont visibles auprès des membres de l’[espace de travail](how-to-manage-workspace.md) d’Azure Machine Learning service. 

Les pipelines utilisent des cibles de calcul à distance pour le calcul et le stockage des données intermédiaires et finales associées à ce pipeline. Les pipelines peuvent lire et écrire des données à partir/vers les emplacements de [Stockage Azure](https://docs.microsoft.com/azure/storage/) pris en charge.

>[!Note]
>Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](http://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prérequis

* [Configurez votre environnement de développement](how-to-configure-environment.md) pour installer le SDK Azure Machine Learning.

* Créez un [espace de travail Azure Machine Learning](how-to-configure-environment.md#workspace) afin de contenir toutes les ressources de votre pipeline. 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>Configurer des ressources Machine Learning

Créez les ressources requises pour exécuter un pipeline :

* Configurez un magasin de données permettant d’accéder aux données requises pour les étapes du pipeline.

* Configurez un objet `DataReference` qui pointe vers les données qui se trouvent dans un magasin de données ou qui sont accessibles à partir de ce biais.

* Configurez les [cibles de calcul](concept-azure-machine-learning-architecture.md#compute-target) sur lesquelles vous souhaitez que les étapes du pipeline s’exécutent.

### <a name="set-up-a-datastore"></a>Configurer un magasin de données
Le magasin de données stocke les données accessibles par le pipeline. Chaque espace de travail dispose d’un magasin de données par défaut. Vous pouvez inscrire des magasins de données supplémentaires. 

Lorsque vous créez votre espace de travail, des éléments [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) et de [stockage d’objets Blob Azure ](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) sont joints à l’espace de travail par défaut. Azure Files représente le magasin de données par défaut pour un espace de travail, mais vous pouvez également utiliser le stockage d’objets Blob en tant que magasin de données. Pour en savoir plus , voir [Quand utiliser des fichiers Azure, des objets blob Azure, des fichiers Azure ou des disques Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Téléchargez les fichiers de données ou les répertoires sur le magasin de données afin que vos pipelines puissent y accéder. Cet exemple utilise le stockage d’objets blob pour le magasin de données :

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
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Les données intermédiaires (ou la sortie d’une étape) sont représentées par un objet [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` est généré en tant que sortie d’une étape et utilisé en tant qu’entrée pour une ou plusieurs étapes ultérieures. `PipelineData` présente une dépendance de données entre les étapes et crée un ordre d’exécution implicite dans le pipeline.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>Configurer le calcul

Dans Azure Machine Learning, le *calcul* (ou la *cible de calcul*) fait référence aux machines ou aux clusters qui effectuent les étapes de calculs de votre pipeline Machine Learning. Par exemple, vous pouvez créer une capacité de calcul Azure Machine Learning pour exécuter vos étapes.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

## <a name="construct-your-pipeline-steps"></a>Composer les étapes de votre pipeline

Vous êtes maintenant prêt à définir une étape de pipeline. De nombreuses étapes intégrées sont disponibles via le SDK Azure Machine Learning. `PythonScriptStep` est la plus simple de ces étapes qui exécute un script Python dans une cible de calcul spécifiée.

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Après avoir défini vos étapes, vous générez le pipeline à l’aide de tout ou partie de ces étapes.

>[!NOTE]
>Aucune donnée ni fichier n’est téléchargé sur le service Azure Machine Learning lorsque vous définissez les étapes ou générez le pipeline.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>Envoyer le pipeline

Lorsque vous envoyez le pipeline, le service Azure Machine Learning vérifie les dépendances pour chaque étape et charge un instantané du répertoire source que vous avez spécifié. Si aucun répertoire source n’est spécifié, le répertoire local actuel est chargé.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

Lorsque vous exécutez un pipeline pour la première fois, Azure Machine Learning :

* Télécharge l’instantané du projet sur la cible de calcul à partir du stockage d’objets Blob associé à l’espace de travail.
* Génère une image docker correspondant à chaque étape du pipeline.
* Télécharge l’image docker de chaque étape sur la cible de calcul à partir du registre de conteneurs.
* Monte le magasin de données si un objet `DataReference` est spécifié dans une étape. Si le montage n’est pas pris en charge, alors les données sont copiées sur la cible de calcul.
* Exécute l’étape dans la cible de calcul spécifiée dans la définition de l’étape. 
* Crée les artefacts tels que les journaux, stdout et stderr,les métriques mesures et la sortie spécifiée par l’étape. Ces artefacts sont ensuite téléchargés et conservés dans le magasin de données par défaut de l’utilisateur.

![Diagramme d’exécution d’une expérience en tant que pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Publier un pipeline

Vous pouvez publier un pipeline afin de l’exécuter ultérieurement avec des entrées différentes. Pour que le point de terminaison REST d’un pipeline déjà publié accepte les paramètres, vous devez paramétrer le pipeline avant sa publication. 

1. Pour créer un paramètre de pipeline, utilisez un objet [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) avec une valeur par défaut.

 ```python
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
published_pipeline1 = pipeline1.publish(
    name="My_Published_Pipeline", 
    description="My Published Pipeline Description")
```

## <a name="run-a-published-pipeline"></a>Exécuter un pipeline publié

Tous les pipelines publiés disposent d’un point de terminaison REST. Celui-ci appelle l’exécution du pipeline à partir de systèmes externes tels que les clients non-Python. Ce point de terminaison active la « répétabilité managée » dans les scénarios de scoring et de nouvel apprentissage.

Pour appeler l’exécution du pipeline précédent, vous avez besoin d’un jeton d’en-tête d’authentification Azure Active Directory comme décrit dans la [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Afficher les résultats

Consultez la liste de tous vos pipelines et les détails relatifs à leur exécution :
1. Connectez-vous au [Portail Azure](https://portal.azure.com/).  

1. [Affichez votre espace de travail](how-to-manage-workspace.md#view-a-workspace) pour trouver la liste des pipelines.
 ![Liste de pipelines Machine Learning](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Sélectionnez un pipeline spécifique pour afficher les résultats de l’exécution.

## <a name="next-steps"></a>Étapes suivantes
- Utilisez [ces blocs-notes Jupyter sur GitHub](https://aka.ms/aml-pipeline-readme) pour explorer plus en détail les pipelines Machine Learning.
- Lisez l’aide relative à la référence SDK des packages [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
