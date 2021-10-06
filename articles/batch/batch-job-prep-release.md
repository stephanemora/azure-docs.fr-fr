---
title: Créer des tâches pour préparer et exécuter des travaux sur des nœuds de calcul
description: Utilisez des tâches de préparation au niveau du travail afin de minimiser le transfert de données vers les nœuds de calcul Azure Batch, et utilisez des tâches de validation pour le nettoyage des nœuds une fois le travail achevé.
ms.topic: how-to
ms.date: 09/10/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 66be694cc05655973afac088066e95c6d7033bb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659737"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Exécuter des tâches de préparation et de validation du travail sur les nœuds de calcul Batch

 Un travail Azure Batch demande souvent une certaine forme de préparation avant l’exécution de ses tâches. Il peut également demander une maintenance une fois les tâches terminées. Par exemple, vous pouvez être amené à télécharger les données d’entrée de tâches communes dans vos nœuds de calcul ou à charger les données de sortie de tâches dans le Stockage Azure une fois le travail terminé. Vous pouvez effectuer ces opérations à l’aide des tâches de **préparation du travail** et de **validation du travail**.

## <a name="what-are-job-preparation-and-release-tasks"></a>En quoi consistent les tâches de préparation et de validation du travail ?

Avant l’exécution des tâches d’un travail, la tâche de préparation du travail s’exécute sur tous les nœuds de calcul destinés à exécuter au moins l’une des tâches. Lorsqu'un travail est terminé, la tâche de validation du travail s'exécute sur chaque nœud dans le pool ayant exécuté au moins une tâche. Comme dans le cas des tâches Batch standard, vous pouvez spécifier une ligne de commande à appeler lors de l’exécution d’une tâche de préparation ou de validation du travail.

Les tâches de préparation et de validation du travail offrent des fonctionnalités de tâche Batch courantes, telles que téléchargement de fichiers ([fichiers de ressources](/dotnet/api/microsoft.azure.batch.jobpreparationtask.resourcefiles)), exécution avec élévation de privilèges, variables d’environnement personnalisées, durée d’exécution maximale, nombre de tentatives et période de rétention des fichiers.

Dans les sections ci-après, vous découvrirez comment utiliser les classes [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) et [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) disponibles dans la bibliothèque [Batch .NET](/dotnet/api/microsoft.azure.batch).

> [!TIP]
> Les tâches de préparation et de validation du travail sont particulièrement utiles dans les environnements de « pool partagé », dans lesquels un pool de nœuds de calcul persiste entre les exécutions d’un travail et est utilisé par de nombreux travaux.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Utilisation des tâches de préparation et de validation du travail

Les tâches de préparation et de validation du travail sont parfaitement adaptées aux opérations suivantes :

- **Téléchargement des données de tâches courantes** : les travaux Batch nécessitent souvent un ensemble commun de données comme entrée pour les tâches du travail. Par exemple, dans les calculs quotidiens de l’analyse des risques, les données de marché sont propres à un travail, mais communes à toutes les tâches de ce travail. Ces données de marché, dont la taille atteint souvent plusieurs gigaoctets, ne doivent être téléchargées qu’une seule fois dans chaque nœud de calcul pour être utilisables par toutes les tâches qui s’exécutent sur un nœud. Utilisez une **tâche de préparation du travail** pour télécharger ces données sur chaque nœud avant l’exécution des autres tâches du travail.

- **Suppression de la sortie des travaux et des tâches** : dans un environnement de « pool partagé » dans lequel les nœuds de calcul d’un pool ne sont pas désactivés entre les travaux, il peut être nécessaire de supprimer les données du travail entre les exécutions afin d’économiser de l’espace disque sur les nœuds ou de respecter les stratégies de sécurité de votre organisation. Utilisez une **tâche de validation du travail** pour supprimer les données téléchargées par une tâche de préparation du travail ou générées pendant l’exécution d’une tâche.

- **Conservation des journaux** : vous voulez peut-être conserver une copie des fichiers journaux générés par les tâches ou peut-être les fichiers de vidage sur incident qui peuvent être générés par les applications ayant échoué. Dans ces cas, utilisez une **tâche de validation du travail** pour compresser et télécharger ces données vers un [compte de stockage Azure](accounts.md#azure-storage-accounts).

## <a name="job-preparation-task"></a>tâche de préparation du travail

Avant l’exécution des tâches d’un travail, Batch exécute la tâche de préparation du travail sur chaque nœud de calcul sur lequel l’exécution d’une tâche est planifiée. Par défaut, Batch attend la fin de la tâche de préparation du travail avant d’exécuter les tâches planifiées pour s’exécuter sur le nœud. Toutefois, vous pouvez configurer le service pour qu'il n'attende pas. Si le nœud redémarre, la tâche de préparation du travail est réexécutée. Vous pouvez aussi désactiver ce comportement. Si vous avez configuré un travail avec une tâche de préparation du travail et une tâche du gestionnaire de travaux, la tâche de préparation du travail est exécutée avant la tâche du gestionnaire de travaux, tout comme avec toutes les autres tâches. La tâche de préparation du travail est toujours exécutée en premier.

La tâche de préparation du travail est uniquement exécutée sur les nœuds sur lesquels l’exécution d’une tâche est planifiée. Ceci empêche l’exécution d’une tâche de préparation inutile si aucune tâche n’est attribuée à un nœud. Cette situation peut survenir lorsque le nombre de tâches pour un travail est inférieur au nombre de nœuds dans un pool. Elle s’applique également si [l’exécution de tâches simultanées](batch-parallel-node-tasks.md) est activée. Dans ce cas, certains nœuds restent inactifs si le nombre de tâches est inférieur au nombre total de tâches simultanées possibles.

> [!NOTE]
> [JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) diffère de [CloudPool.StartTask](/dotnet/api/microsoft.azure.batch.cloudpool.starttask) dans la mesure où JobPreparationTask s’exécute au début de chaque travail, tandis que StartTask s’exécute uniquement lorsqu’un nœud de calcul rejoint un pool ou redémarre.

## <a name="job-release-task"></a>tâche de validation du travail

Quand un travail est marqué comme terminé, la tâche de validation du travail s’exécute sur chaque nœud dans le pool ayant exécuté au moins une tâche. Vous marquez un travail comme terminé en émettant une requête de fin. Cette requête définit ensuite l’état du travail sur *arrêt*, met fin à toutes les tâches actives ou en cours d’exécution associées au travail, puis exécute la tâche de validation du travail. Le travail passe ensuite à l'état *terminé* .

> [!NOTE]
> La suppression d’un travail exécute également la tâche de validation du travail. Toutefois, si un travail a déjà été arrêté, la tâche de validation n’est pas exécutée une seconde fois si ce travail est supprimé par la suite.

Les tâches de validation du travail peuvent s’exécuter pendant un maximum de 15 minutes avant d’être terminées par le service Batch. Pour plus d’informations, voir la [Documentation de référence sur l’API REST](/rest/api/batchservice/job/add#jobreleasetask).

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Tâches de préparation et de validation du travail avec Batch.NET

Pour utiliser une tâche de préparation du travail, affectez un objet [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) à la propriété [CloudJob.JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) de votre travail. De même, pour utiliser une tâche de validation du travail, initialisez la propriété [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) et affectez-la à la propriété [CloudJob.JobReleaseTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) de votre travail.

Dans cet extrait de code, `myBatchClient` est une instance de [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient), et `myPool` est un pool existant dans le compte Batch.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Comme mentionné ci-dessus, la tâche de validation est exécutée lorsqu’un travail est arrêté ou supprimé. Pour arrêter un travail, utilisez [JobOperations.TerminateJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.terminatejobasync). Pour supprimer un travail, utilisez [JobOperations.DeleteJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.deletejobasync). Généralement, vous arrêtez ou supprimez un travail lorsque les tâches de ce dernier sont terminées ou qu’un délai d’expiration que vous avez défini a été atteint.

```csharp
// Terminate the job to mark it as completed; this will initiate the
// job release task on any node that executed job tasks. Note that the
// job release task is also executed when a job is deleted, so you don't
// have to call Terminate if you delete jobs after task completion.

await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemple de code sur GitHub

Pour découvrir les tâches de préparation et de validation du travail en action, consultez l’exemple de projet [JobPrepRelease](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease) sur GitHub. Cette application de console effectue les opérations suivantes :

1. Crée un pool avec deux nœuds.
1. Crée un travail avec des tâches de préparation du travail, de validation et standard.
1. Exécute la tâche de préparation du travail qui écrit d'abord l'ID de nœud dans un fichier texte dans le répertoire « partagé » d'un nœud.
1. Exécute une tâche sur chaque nœud qui écrit son ID de tâche dans le même fichier texte.
1. Lorsque toutes les tâches sont terminées (ou que le délai d'attente est atteint), imprime le contenu du fichier texte de chaque nœud dans la console.
1. Lorsque le travail est terminé, exécute la tâche de validation du travail pour supprimer le fichier du nœud.
1. Imprime les codes de sortie des tâches de préparation et de validation du travail pour chaque nœud sur lequel elles sont exécutées.
1. Interrompt l'exécution pour permettre la confirmation de la suppression du pool et/ou du travail.

Le résultat de l'exemple d'application ressemble à ce qui suit :

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> En raison de la variabilité des heures de création et de démarrage des nœuds dans un nouveau pool (certains nœuds sont prêts pour les tâches avant d’autres), vous risquez d’obtenir un résultat différent. En particulier, étant donné que les tâches s’exécutent rapidement, l’un des nœuds du pool peut exécuter l’ensemble des tâches du travail. Si cela se produit, vous remarquerez que les tâches de préparation et de validation du travail n’existent pas pour le nœud qui n’a exécuté aucune tâche.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspection des tâches de préparation et de validation du travail dans le Portail Azure

Vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour voir les propriétés du travail et de ses tâches. Après avoir exécuté l’exemple d’application, vous pouvez également télécharger le fichier texte partagé qui est modifié par les tâches du travail.

La capture d’écran ci-après montre le **panneau Tâches de préparation** du portail Azure. Accédez aux propriétés *JobPrepReleaseSampleJob* une fois les tâches terminées (mais avant la suppression de votre travail et du pool), puis cliquez sur **Tâches de préparation** ou sur **Tâches de fin** pour en visualiser les propriétés.

:::image type="content" source="media/batch-job-prep-release/portal-jobprep-01.png" alt-text="Capture d’écran montrant les propriétés de la tâche de préparation du travail dans le portail Azure.":::

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [la vérification des erreurs pour les travaux et les tâches](batch-job-task-error-checking.md).
- Découvrez comment utiliser des [packages d’application](batch-application-packages.md) pour préparer des nœuds de calcul Batch pour l’exécution de tâches.
- Explorez les différentes façons de [copier des données et des applications sur des nœuds de calcul Batch](batch-applications-to-pool-nodes.md).
- Découvrez comment utiliser la [bibliothèque de conventions de fichier Azure Batch](batch-task-output.md#use-the-batch-file-conventions-library-for-net) pour conserver les journaux et les autres données de sortie des travaux et des tâches.
