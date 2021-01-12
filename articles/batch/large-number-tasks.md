---
title: Envoyer un grand nombre de tâches dans un travail Batch
description: Découvrez comment envoyer efficacement un très grand nombre de tâches dans un même travail Azure Batch.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831514"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Envoyer un grand nombre de tâches dans un travail Batch

Lorsque vous exécutez des charges de travail Azure Batch à grande échelle, vous pouvez avoir besoin d’envoyer des dizaines ou des centaines de milliers de tâches, voire davantage, au sein d’un même travail.

Cet article vous montre comment envoyer un grand nombre de tâches dans un même travail Batch, avec un débit considérablement accru. Une fois envoyées, les tâches entrent dans la file d’attente Batch en vue d’être traitées dans le pool que vous avez spécifié pour le travail.

## <a name="use-task-collections"></a>Utiliser des collections de tâches

Quand vous ajoutez un grand nombre de tâches, utilisez les méthodes ou les surcharges appropriées fournies par les API Batch pour les ajouter sous forme de *collection* plutôt qu’une à la fois. En règle générale, pour créer une collection de tâches, vous définissez les tâches pendant que vous parcourez un ensemble de fichiers d’entrée ou de paramètres pour votre travail.

La taille maximale de la collection de tâches qu’il est possible d’ajouter dans un appel dépend de l’API Batch que vous utilisez.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>API autorisant des collections de 100 tâches au maximum

Dans ces API Batch, les collections sont limitées à 100 tâches. Cette limite peut être inférieure selon la taille des tâches (par exemple, si les tâches comprennent un grand nombre de fichiers de ressources ou de variables d’environnement).

- [REST API](/rest/api/batchservice/task/addcollection)
- [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [API Node.js](/javascript/api/@azure/batch/task)

Quand vous utilisez ces API, vous devez fournir une logique permettant de diviser le nombre de tâches afin de respecter la limite et de gérer les erreurs et nouvelles tentatives en cas d’échec de l’ajout de tâches. Si une collection de tâches est trop volumineuse, la requête génère une erreur et doit être retentée avec une collection contenant moins de tâches.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>API autorisant des collections d’un plus grand nombre de tâches

Les autres API Batch prennent en charge des collections de tâches beaucoup plus volumineuses, qui sont seulement limitées par la disponibilité de la RAM sur le client d’envoi. Ces API gèrent de manière transparente la division de la collection en plusieurs « blocs » de tâches pour les API de niveau inférieur ainsi que les nouvelles tentatives en cas d’échec d’ajout de tâches.

- [API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [API Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Extension Batch d’Azure CLI](batch-cli-templates.md) avec des modèles d’interface de ligne de commande Batch
- [Extension du SDK Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Augmenter le débit des envois de tâches

L’ajout d’une grande collection de tâches à un travail peut prendre un certain temps. Par exemple, l’ajout de 20 000 tâches par le biais de l’API .NET peut prendre jusqu’à une minute. En fonction de l’API Batch et de votre charge de travail, vous pouvez améliorer le débit d’envoi des tâches en modifiant un ou plusieurs des éléments suivants.

### <a name="task-size"></a>Taille de la tâche

L’ajout de tâches volumineuses prend plus de temps que l’ajout de tâches plus petites. Pour réduire la taille de chaque tâche d’une collection, vous pouvez simplifier la ligne de commande des tâches, réduire le nombre de variables d’environnement ou gérer plus efficacement les exigences d’exécution des tâches.

Par exemple, au lieu d’utiliser un grand nombre de fichiers de ressources, installez les dépendances des tâches à l’aide d’une [tâche de démarrage](jobs-and-tasks.md#start-task) dans le pool. Vous pouvez également utiliser un [package d’application](batch-application-packages.md) ou un [conteneur Docker](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Nombre d’opérations parallèles

Selon l’API Batch, vous pouvez augmenter le débit en augmentant le nombre maximal d’opérations simultanées que peut exécuter le client Batch. Configurez ce paramètre à l’aide de la propriété [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) dans l’API .NET, ou du paramètre `threads` des méthodes telles que [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations)dans l’extension du SDK Python pour Batch (cette propriété n’est pas disponible dans la version native du SDK Python pour Batch).

Par défaut, cette propriété est définie sur 1. Toutefois, vous pouvez définir une valeur supérieure pour améliorer le débit des opérations. L’inconvénient d’un débit élevé est la grande consommation de bande passante réseau et la baisse des performances de l’UC. Le débit des tâches peut augmenter jusqu’à 100 fois le `MaxDegreeOfParallelism` ou les `threads`. Dans la pratique, vous devez définir le nombre d’opérations simultanées sur une valeur inférieure à 100.

 L’extension Batch d’Azure CLI et les modèles Batch augmentent automatiquement le nombre d’opérations simultanées en fonction du nombre de cœurs disponibles. Cependant, cette propriété n’est pas configurable dans l’interface CLI.

### <a name="http-connection-limits"></a>Limites de connexions HTTP

Un nombre élevé de connexions HTTP simultanées peut limiter les performances du client Batch quand il ajoute un grand nombre de tâches. Certaines API limitent le nombre de connexions HTTP. Lorsque vous développez avec l’API .NET, par exemple, la propriété [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) est définie sur 2 par défaut. Nous vous recommandons de définir un nombre supérieur au nombre d’opérations parallèles, ou s’en approchant.

## <a name="example-batch-net"></a>Exemple : .NET Batch

Les extraits de code C# suivants montrent les paramètres à configurer lorsque vous ajoutez un grand nombre de tâches à l’aide de l’API .NET Batch.

Pour accroître le débit des tâches, augmentez la valeur de la propriété [MaxDegreeofParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) de [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Par exemple :

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Ajoutez une collection de tâches au travail à l’aide de la surcharge appropriée de la méthode [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) ou [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
). Par exemple :

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Exemple : extension Batch pour CLI

À l’aide des extensions Batch pour Azure CLI et des [modèles d’interface CLI Batch](batch-cli-templates.md), créez un fichier JSON de modèle de travail comprenant une [fabrique de tâches](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). La fabrique de tâches configure une collection de tâches associées pour un travail, à partir d’une seule définition de tâche.  

Voici un exemple de modèle pour un travail de balayage paramétrique unidimensionnel comprenant un grand nombre de tâches (en l’occurrence, 250 000). La ligne de commande des tâches est une simple commande `echo`.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Pour exécuter un travail avec ce modèle, consultez [Utiliser des modèles d’interface de ligne de commande Azure Batch et le transfert de fichiers](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exemple : Extension du SDK Python

Pour utiliser l’extension du SDK Python pour Azure Batch, commencez par installer le SDK Python et l’extension :

```
pip install azure-batch
pip install azure-batch-extensions
```

Configurez un `BatchExtensionsClient` qui utilise l’extension du SDK :

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Créez une collection de tâches à ajouter à un travail. Par exemple :

```python
tasks = list()
# Populate the list with your tasks
...
```

Ajoutez la collection de tâches à l’aide de [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Définissez le paramètre `threads` pour augmenter le nombre d’opérations simultanées :

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

L’extension du SDK Python pour Batch permet également d’ajouter des paramètres de tâche à l’aide d’une spécification JSON pour une fabrique de tâches. Par exemple, configurez les paramètres d'un travail de balayage paramétrique semblable à celui de l'exemple précédent de modèle d'interface CLI pour Batch :

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Ajoutez les paramètres au travail. Définissez le paramètre `threads` pour augmenter le nombre d’opérations simultanées :

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’utilisation de l’extension Batch d’Azure CLI avec des [modèles d’interface de ligne de commande Batch](batch-cli-templates.md)
- En savoir plus sur [l’extension du SDK Python pour Batch](https://pypi.org/project/azure-batch-extensions/)
- Consultez les [bonnes pratiques pour Azure Batch](best-practices.md).
