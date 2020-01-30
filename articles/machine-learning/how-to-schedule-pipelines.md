---
title: Planifier des pipelines Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Planifier des pipelines Machine Learning à l’aide du kit de développement logiciel (SDK) Azure Machine Learning pour Python. Les pipelines planifiés vous permettent d’automatiser les tâches de routine et fastidieuses, telles que le traitement des données, la formation et la surveillance.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: 1766b536043d8c404addb1877aa3ef9b57344ef4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722252"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Planifier des pipelines Machine Learning avec le kit de développement logiciel (SDK) Azure Machine Learning pour Python

Dans cet article, vous allez apprendre à planifier un pipeline de manière programmatique pour qu’il s’exécute sur Azure. Vous pouvez choisir de créer une planification basée sur la durée calendaire ou sur les modifications du système de fichiers. Les calendriers basés sur la durée peuvent être utilisés pour prendre en charge des tâches de routine, telles que la surveillance de la dérive des données. Les planifications basées sur les modifications peuvent être utilisées pour réagir à des modifications irrégulières ou imprévisibles, telles que de nouvelles données téléchargées ou d’anciennes données modifiées. Après avoir appris à créer des planifications, vous apprendrez comment les récupérer et les désactiver.

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree).

* Environnement Python dans lequel le kit de développement logiciel (SDK) Azure Machine Learning pour Python est installé. Pour plus d’informations, consultez [Créez et gérez des environnements réutilisables pour la formation et le déploiement avec Azure Machine Learning.](how-to-use-environments.md)

* Un espace de travail Machine Learning avec un pipeline publié. Vous pouvez utiliser l’option intégrée [Créer et exécuter des pipelines Machine Learning avec le kit de développement logiciel (SDK) Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Initialiser l’espace de travail et obtenir des données

Pour planifier un pipeline, vous avez besoin d’une référence à votre espace de travail, de l’identificateur de votre pipeline publié et du nom de l’expérience dans laquelle vous souhaitez créer la planification. Vous pouvez obtenir ces valeurs avec le code suivant :

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Créer une planification

Pour exécuter un pipeline de manière récurrente, vous allez créer une planification. Une `Schedule`associe un pipeline, une expérience et un déclencheur. Le déclencheur peut être un `ScheduleRecurrence` qui décrit l’attente entre les exécutions ou un chemin d’accès aux magasins de banques qui spécifie un répertoire à surveiller pour les modifications. Dans les deux cas, vous avez besoin de l’identificateur du pipeline et du nom de l’expérience dans laquelle créer la planification.

### <a name="create-a-time-based-schedule"></a>Créer une planification basée sur la durée

Le constructeur `ScheduleRecurrence` a un argument `frequency` obligatoire qui doit correspondre à l’une des chaînes suivantes : « Minute », « Heure », « Jour », « Semaine » ou « Mois ». Il exige également un argument `interval` entier spécifiant le nombre d’unités `frequency` qui doivent s’écouler entre chaque début de planification. Les arguments facultatifs vous permettent d’être plus précis sur les heures de démarrage, comme détaillé dans les [documents du kit de développement logiciel (SDK) ScheduleRecurrence](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Créer une `Schedule` qui commence une exécution toutes les 15 minutes :

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Créer une planification basée sur les modifications

Les pipelines qui sont déclenchés par les modifications de fichiers peuvent être plus efficaces que les planifications basées sur la durée. Par exemple, si vous souhaitez exécuter une étape de pré-traitement lors de la modification d’un fichier ou si un nouveau fichier est ajouté à un répertoire de données. Vous pouvez surveiller les modifications apportées à un magasin de donnée ou à des modifications dans un répertoire spécifique au sein du magasin de données. Si vous surveillez un répertoire spécifique, les modifications au sein des sous-répertoires de ce répertoire ne déclencheront _pas_ d’exécution.

Pour créer une `Schedule` réactive aux fichiers, vous devez définir le paramètre `datastore` dans l’appel à [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Pour surveiller un dossier, définissez l’argument `path_on_datastore`.

L’argument `polling_interval` vous permet de spécifier, en minutes, la fréquence de vérification des modifications dans le magasin de données.

Si le pipeline a été construit avec un [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py), vous pouvez définir cette variable en fonction du nom du fichier modifié en définissant l’argument `data_path_parameter_name`.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Arguments facultatifs lors de la création d’une planification

En plus des arguments susmentionnés, vous pouvez définir l’argument `status` sur `"Disabled"` pour créer une planification inactive. Enfin, `continue_on_step_failure` vous permet de transmettre une valeur booléenne qui remplacera le comportement d’échec par défaut du pipeline.

## <a name="view-your-scheduled-pipelines"></a>Afficher vos tâches planifiées

Dans votre navigateur web, accédez à Azure Machine Learning. Dans la section **Points de terminaison** du panneau de navigation, choisissez **Points de terminaison de pipeline**. Vous obtenez une liste des pipelines publiés dans l’espace de travail.

![Page Pipelines d’AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Dans cette page, vous pouvez consulter des informations de résumé sur tous les pipelines dans l’espace de travail : noms, descriptions, état, etc. Découvrez-en plus en cliquant sur votre pipeline. Dans la page affichée, découvrez plus de détails sur votre pipeline et accédez à des exécutions individuelles.

## <a name="deactivate-the-pipeline"></a>Désactiver le pipeline

Si vous votre `Pipeline` est publié mais non planifié, vous pouvez le désactiver avec les éléments suivants :

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Si le pipeline est planifié, vous devez d’abord annuler la planification. Récupérez l’identificateur de la planification sur le portail ou en exécutant :

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Une fois que vous avez trouvé le `schedule_id` à désactiver, exécutez :

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Si vous réexécutez ensuite `Schedule.list(ws)`, vous devez obtenir une liste vide.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez utilisé le kit de développement logiciel (SDK) Azure Machine Learning pour Python pour planifier un pipeline de deux façons différentes. Une planification est récurrente en fonction de la durée écoulée. L’autre planification s’exécute si un fichier est modifié sur un `Datastore` spécifié ou dans un répertoire de ce magasin. Vous avez vu comment utiliser le portail pour examiner le pipeline et les exécutions individuelles. Enfin, vous avez appris à désactiver une planification afin que le pipeline cesse de s’exécuter.

Pour plus d'informations, consultez les pages suivantes :

> [!div class="nextstepaction"]
> [Utiliser des pipelines Azure Machine Learning pour le scoring par lots](tutorial-pipeline-batch-scoring-classification.md)

* En savoir plus sur les [pipelines](concept-ml-pipelines.md)
* En savoir plus sur [l’exploration d’Azure Machine Learning avec Jupyter](samples-notebooks.md)
