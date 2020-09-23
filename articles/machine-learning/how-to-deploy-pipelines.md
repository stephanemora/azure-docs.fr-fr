---
title: Publier des pipelines ML
titleSuffix: Azure Machine Learning
description: Exécutez des flux de travail Machine Learning avec des pipelines Machine Learning et le SDK Azure Machine Learning pour Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: ddc8186e85001a2a3ed2ed9f57b8f025133ef16a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897750"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publier et suivre des pipelines Machine Learning



Cet article vous explique comment partager un pipeline Machine Learning avec vos collègues ou vos clients.

Les pipelines Machine Learning sont des flux de travail réutilisables pour les tâches de Machine Learning. L’un des avantages des pipelines est une collaboration accrue. Vous pouvez également créer des versions du pipeline, ce qui permet aux clients d’utiliser le modèle actuel pendant que vous travaillez sur une nouvelle version. 

## <a name="prerequisites"></a>Prérequis

* Créer un [espace de travail Azure Machine Learning](how-to-manage-workspace.md) afin de contenir toutes les ressources de votre pipeline

* [Configurer votre environnement de développement](how-to-configure-environment.md) pour installer le SDK Azure Machine Learning ou utilisez une [instance de calcul Azure Machine Learning](concept-compute-instance.md) avec le SDK déjà installé

* Créez et exécutez un pipeline Machine Learning, par exemple en suivant le [Tutoriel : Créer un pipeline Azure Machine Learning pour le scoring par lots](tutorial-pipeline-batch-scoring-classification.md). Pour d’autres options, consultez [Créer et exécuter des pipelines Machine Learning avec le kit SDK Azure Machine Learning](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>Publier un pipeline

Une fois que vous disposez d’un pipeline opérationnel, vous pouvez publier un pipeline pour qu’il s’exécute avec des entrées différentes. Pour que le point de terminaison REST d’un pipeline déjà publié accepte des paramètres, vous devez configurer votre pipeline afin d’utiliser des objets `PipelineParameter` pour les arguments qui varient.

1. Pour créer un paramètre de pipeline, utilisez un objet [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) avec une valeur par défaut.

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
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publiez ce pipeline. Il acceptera un paramètre en cas d’appel.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Exécuter un pipeline publié

Tous les pipelines publiés disposent d’un point de terminaison REST. Avec le point de terminaison de pipeline, vous pouvez déclencher une exécution du pipeline à partir de n’importe quel système externe, notamment des clients autres que Python. Ce point de terminaison active la « répétabilité managée » dans les scénarios de scoring et de nouvel apprentissage.

Pour appeler l’exécution du pipeline précédent, vous avez besoin d’un jeton d’en-tête d’authentification Azure Active Directory. L’obtention de ce jeton est décrite dans les informations de référence sur la [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) et dans le notebook [Authentification dans Azure Machine Learning](https://aka.ms/pl-restep-auth).

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Créer un point de terminaison de pipeline en versions gérées

Vous pouvez créer un point de terminaison de pipeline avec plusieurs pipelines publiés derrière. Cela vous procure un point de terminaison REST au moment de l’itération et de la mise à jour de vos pipelines Machine Learning.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Soumettre un travail à un point de terminaison de pipeline

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

## <a name="use-published-pipelines-in-the-studio"></a>Utiliser des pipelines publiés dans le studio

Vous pouvez également exécuter un pipeline publié à partir du studio :

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com).

1. [Affichez votre espace de travail](how-to-manage-workspace.md#view).

1. Sur la gauche, sélectionnez **Points de terminaison**.

1. En haut, sélectionnez **Points de terminaison de pipeline**.
 ![Liste de pipelines publiés Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Sélectionnez un pipeline spécifique pour exécuter, utiliser ou examiner les résultats des exécutions précédentes du point de terminaison du pipeline.

## <a name="disable-a-published-pipeline"></a>Désactiver un pipeline publié

Pour masquer un pipeline de votre liste de pipelines publiés, vous le désactivez soit dans le studio, soit à partir du SDK :

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Vous pouvez le réactiver avec `p.enable()`. Pour plus d’informations, consultez la référence de la [classe PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true).

## <a name="next-steps"></a>Étapes suivantes

- Utilisez [ces blocs-notes Jupyter sur GitHub](https://aka.ms/aml-pipeline-readme) pour explorer plus en détail les pipelines Machine Learning.
- Consultez l’aide relative à la référence SDK des packages [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) et [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true).
- Consultez le [guide pratique](how-to-debug-pipelines.md) pour obtenir des conseils sur le débogage et la résolution des problèmes de pipelines.
