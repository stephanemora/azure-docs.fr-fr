---
title: Exécuter des applications MPI avec des tâches multi-instances
description: Découvrez comment exécuter des applications MPI (Message Passing Interface) en utilisant des tâches de type multi-instances dans Azure Batch.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: b25008e4001676d1289f8a035cad972084d025bc
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108126234"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Batch

Les tâches multi-instances vous permettent d’exécuter une tâche Azure Batch sur plusieurs nœuds de calcul simultanément. Ces tâches permettent de mettre en œuvre des scénarios de calcul haute performance tels que les applications MPI (Message Passing Interface) dans Batch. Dans cet article, vous découvrez comment exécuter des tâches multi-instances à l’aide de la bibliothèque [Batch .NET](/dotnet/api/microsoft.azure.batch).

> [!NOTE]
> Bien que les exemples de cet article portent sur Batch .NET, MS-MPI et les nœuds de calcul Windows, les concepts de tâches multi-instances présentés ici s’appliquent à d’autres plateformes et technologies (Python et Intel MPI sur des nœuds Linux, par exemple).

## <a name="multi-instance-task-overview"></a>Présentation des tâches multi-instances

Dans Azure Batch, chaque tâche est normalement exécutée sur un nœud de calcul unique : vous soumettez plusieurs tâches à un travail, et le service Azure Batch planifie l’exécution de chacune d’entre elles sur un nœud. Toutefois, en configurant les **paramètres multi-instances** d’une tâche, vous pouvez indiquer à la place à Batch de créer une tâche principale et quelques tâches subordonnées qui sont ensuite exécutées sur plusieurs nœuds.

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="Diagramme illustrant une vue d’ensemble des paramètres multi-instances.":::

Lorsque vous envoyez à un travail une tâche dotée de paramètres multi-instances, Azure Batch exécute plusieurs étapes uniques aux tâches multi-instances :

1. Le service Batch crée une **tâche principale** et plusieurs **tâches subordonnées** selon les paramètres multi-instances. Le nombre total de tâches (tâche principale, ainsi que toutes les tâches subordonnées) correspond au nombre **d’instances** (nœuds de calcul) que vous spécifiez dans les paramètres multi-instances.
2. Batch désigne l’un des nœuds de calcul comme **principal** et planifie la tâche principale à exécuter sur le nœud principal. Il planifie les tâches subordonnées à exécuter sur le reste des nœuds de calcul alloués à la tâche multi-instance, une tâche subordonnée par nœud.
3. La tâche principale et toutes les tâches subordonnées téléchargent les **fichiers de ressources communs** que vous indiquez dans les paramètres multi-instances.
4. Une fois les fichiers de ressources communs téléchargés, la tâche principale et les tâches subordonnées exécutent la **commande de coordination** que vous indiquez dans les paramètres multi-instances. La commande de coordination sert généralement à préparer des nœuds en vue de l’exécution de la tâche. Cela peut impliquer de démarrer les services d’arrière-plan (par exemple de [Microsoft MPI](/message-passing-interface/microsoft-mpi)`smpd.exe`) et de vérifier que les nœuds sont prêts à traiter les messages entre nœuds.
5. La tâche principale exécute la **commande d’application** du nœud principal *une fois que* la tâche principale et toutes les tâches subordonnées ont fini d’exécuter la commande de coordination. La commande d’application est la ligne de commande de la tâche multi-instances. Elle est exécutée uniquement par la tâche principale. Dans une solution basée sur [MS-MPI](/message-passing-interface/microsoft-mpi), c’est là où vous exécutez votre application prenant en charge MPI au moyen du fichier `mpiexec.exe`.

> [!NOTE]
> Même si elle est différente d’un point de vue fonctionnel, la « tâche multi-instances » n’est pas un type de tâche unique comme [StartTask](/dotnet/api/microsoft.azure.batch.starttask) ou [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask). La tâche multi-instances est simplement une tâche Batch standard ([CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) dans Batch .NET) dont les paramètres multi-instances ont été configurés. Dans cet article, nous parlons de **tâche multi-instances**.

## <a name="requirements-for-multi-instance-tasks"></a>Configuration requise pour les tâches multi-instances

Les tâches multi-instances nécessitent un pool avec **communication entre les nœuds activée** et **exécution de tâches simultanées désactivée**. Pour désactiver l’exécution des tâches simultanées, définissez la propriété [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) sur 1.

> [!NOTE]
> Batch [limite](batch-quota-limit.md#pool-size-limits) la taille d’un pool pour lequel la communication entre nœuds est activée.

Cet extrait de code montre comment créer un pool pour les tâches multi-instances à l’aide de la bibliothèque Batch .NET.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Si vous essayez d’exécuter une tâche multi-instances dans un pool dont la communication entre les nœuds a été désactivée ou avec une valeur *taskSlotsPerNode* supérieure à 1, la tâche ne sera jamais planifiée et restera indéfiniment à l’état « actif ».

### <a name="use-a-starttask-to-install-mpi"></a>Utiliser une tâche de début pour installer MPI

Pour exécuter des applications MPI avec une tâche multi-instances, vous devez d’abord installer une implémentation MPI (MS-MPI ou Intel MPI par exemple) sur les nœuds de calcul du pool. C’est là une bonne occasion d’utiliser une tâche [StartTask](/dotnet/api/microsoft.azure.batch.starttask) qui s’exécute à chaque fois qu’un nœud rejoint un pool ou est redémarré. Cet extrait de code crée une tâche StartTask qui spécifie le package d’installation de MS-MPI comme un [fichier de ressources](/dotnet/api/microsoft.azure.batch.resourcefile). La ligne de commande de la tâche de début est exécutée une fois le fichier de ressources téléchargé sur le nœud. Dans ce cas, la ligne de commande effectue une installation sans assistance de MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Accès direct à la mémoire à distance (RDMA)

Lorsque vous utilisez une [Taille compatible RDMA](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) comme A9 pour les nœuds de calcul dans votre pool Batch, votre application MPI peut tirer parti du réseau d’accès direct à la mémoire à distance (RDMA) haute performance d’Azure.

Recherchez les tailles spécifiées comme « compatibles RDMA » dans [Tailles pour les machines virtuelles dans Azure](../virtual-machines/sizes.md) (pour les pools VirtualMachineConfiguration) ou [Tailles pour les services cloud](../cloud-services/cloud-services-sizes-specs.md) (pour les pools CloudServicesConfiguration).

> [!NOTE]
> Pour tirer parti de RDMA sur des [nœuds de calcul Linux](batch-linux-nodes.md), vous devez utiliser **Intel MPI** sur ces derniers.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Créer une tâche multi-instances avec Batch.NET

Maintenant que nous avons abordé les exigences du pool et l’installation du package MPI, nous allons créer la tâche multi-instances. Dans cet extrait de code, nous créons une [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) standard, puis nous configurons sa propriété [MultiInstanceSettings](/dotnet/api/microsoft.azure.batch.cloudtask). Comme mentionné ci-dessus, la tâche multi-instances n’est pas un type de tâche distinct, mais une tâche Batch standard configurée avec des paramètres multi-instances.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Tâche principale et tâches subordonnées

Lorsque vous créez les paramètres multi-instances d’une tâche, vous indiquez le nombre de nœuds de calcul qui vont exécuter la tâche. Lorsque vous soumettez la tâche à un travail, le service Batch crée une tâche **principale** et suffisamment de **tâches subordonnées** pour le nombre de nœuds indiqué.

Ces tâches se voient affecter un identifiant entier allant de 0 à *numberOfInstances* - 1. La tâche dont l’identifiant est 0 est la tâche principale, tandis que tous les autres identifiants correspondent aux tâches subordonnées. Par exemple, si vous créez les paramètres multi-instances suivants d’une tâche, la tâche principale a pour identifiant 0 et les tâches subordonnées, des identifiants allant de 1 à 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nœud principal

Quand vous soumettez une tâche multi-instance, le service Batch désigne l’un des nœuds de calcul comme nœud « principal » et planifie la tâche principale à exécuter sur le nœud principal. Les tâches subordonnées sont planifiées pour s’exécuter sur le reste des nœuds alloués à la tâche multi-instance.

## <a name="coordination-command"></a>commande de coordination

La **commande de coordination** est exécutée à la fois par la tâche principale et les tâches subordonnées.

L’appel de la commande de coordination bloque : Azure Batch exécute uniquement la commande d’application lorsque la commande de coordination a été renvoyée avec succès pour toutes les tâches subordonnées. Par conséquent, la commande de coordination doit démarrer tous les services d’arrière-plan requis, vérifier qu’ils sont prêts à être utilisés, puis se fermer. Par exemple, cette commande coordination pour une solution qui utilise la version 7 de MS-MPI démarre le service SMPD sur le nœud, puis se ferme :

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Notez l’utilisation de `start` dans cette commande de coordination. Cela est nécessaire, car l’application `smpd.exe` n’est pas immédiatement renvoyée après l’exécution. Sans l’utilisation de la commande start, cette commande de coordination ne serait pas renvoyée et bloquerait par conséquent l’exécution de la commande d’application.

## <a name="application-command"></a>Commande d’application

Une fois que la tâche principale et toutes les tâches subordonnées ont terminé d’exécuter la commande de coordination, la ligne de commande de la tâche multi-instances est exécutée par la tâche principale *uniquement*. Nous appelons cette ligne de commande la **commande d’application** pour la différencier de la commande de coordination.

Pour les applications MS-MPI, utilisez la commande d’application pour exécuter votre application MPI avec `mpiexec.exe`. Par exemple, voici une commande d’application pour une solution qui utilise la version 7 de MS-MPI :

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Étant donné que `mpiexec.exe` de MS-MPI utilise la variable `CCP_NODES` par défaut (voir [Variables d’environnement](#environment-variables)), l’exemple de ligne de commande d’application ci-dessus l’exclut.

## <a name="environment-variables"></a>Variables d'environnement

Batch crée plusieurs [variables d’environnement](batch-compute-node-environment-variables.md) propres aux tâches multi-instances sur les nœuds de calcul affectés à une tâche multi-instance. Vos lignes de commande d’application et de coordination peuvent référencer ces variables d’environnement, de même que les scripts et les programmes qu’elles exécutent.

Les variables d’environnement suivantes sont créées par le service Batch pour les tâches multi-instances :

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Pour plus d’informations sur les variables d’environnement des nœuds de calcul Batch, notamment leur contenu et leur visibilité, consultez la page [Variables d’environnement des nœuds de calcul](batch-compute-node-environment-variables.md).

> [!TIP]
> L’[exemple de code Batch Linux MPI](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) contient un exemple d’utilisation de plusieurs d’entre elles.

## <a name="resource-files"></a>Fichiers de ressources

Il existe deux ensembles de fichiers de ressources à prendre en compte pour les tâches multi-instances : les **fichiers de ressources communs** que *toutes* les tâches téléchargent (tâche principale et tâches subordonnées) et les **fichiers de ressources** indiqués pour la tâche multi-instances elle-même que *seule la tâche principale* télécharge.

Vous pouvez indiquer un ou plusieurs **fichiers de ressources communs** dans les paramètres multi-instances d’une tâche. Ces fichiers de ressources communs sont téléchargés à partir du [Stockage Azure](../storage/common/storage-introduction.md) dans le **répertoire partagé de tâche** de chaque nœud, par la tâche principale et toutes les tâches subordonnées. Vous pouvez accéder au répertoire partagé de la tâche à partir de l’application et des lignes de commande de coordination à l’aide de la variable d’environnement `AZ_BATCH_TASK_SHARED_DIR` . Le chemin d’accès `AZ_BATCH_TASK_SHARED_DIR` est identique sur tous les nœuds alloués à la tâche multi-instance. Vous pouvez par conséquent partager une commande de coordination unique entre la tâche principale et toutes les tâches subordonnées. Batch ne « partage » pas le répertoire dans le sens de l’accès à distance, mais vous pouvez l’utiliser comme point de montage ou de partage comme cela a été mentionné dans le conseil sur les variables d’environnement.

Les fichiers de ressources que vous spécifiez pour la tâche multi-instance sont téléchargés dans le répertoire de travail de la tâche, `AZ_BATCH_TASK_WORKING_DIR` par défaut. Comme nous l’avons mentionné, contrairement aux fichiers de ressources communs, seule la tâche principale télécharge les fichiers de ressources spécifiés pour la tâche multi-instances.

> [!IMPORTANT]
> Veillez à toujours utiliser les variables d’environnement `AZ_BATCH_TASK_SHARED_DIR` et `AZ_BATCH_TASK_WORKING_DIR` pour faire référence à ces répertoires dans vos lignes de commande. N’essayez pas de construire les chemins d’accès manuellement.

## <a name="task-lifetime"></a>Durée de vie de la tâche

La durée de vie de la tâche principale contrôle la durée de vie de la tâche multi-instances tout entière. Lorsque la tâche principale s’arrête, toutes les tâches subordonnées s’arrêtent aussi. Le code de sortie de la tâche principale est le code de sortie de la tâche. Il est donc utilisé pour déterminer la réussite ou l’échec de la tâche pour toutes nouvelles tentatives.

Si l’une des tâches subordonnées échoue, par exemple en se fermant avec un code de retour différent de zéro, la tâche multi-instances tout entière échoue. La tâche multi-instances s’arrête ensuite puis reprend, dans la limite du nombre de nouvelles tentatives défini.

Quand vous supprimez une tâche multi-instances, la tâche principale et toutes les tâches subordonnées sont également supprimées par le service Azure Batch. Tous les répertoires des tâches subordonnées et leurs fichiers sont supprimés des nœuds de calcul, à l’image d’une tâche standard.

Les [TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) d’une tâche multi-instances, par exemple les propriétés [MaxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount), [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime) et [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime), sont honorées en l’état pour une tâche standard, puis s’appliquent à la tâche principale et à toutes les tâches subordonnées. Toutefois, si vous modifiez la propriété RetentionTime après avoir ajouté la tâche multi-instances au travail, cette modification s’applique uniquement à la tâche principale et toutes les tâches subordonnées continuent à utiliser le RetentionTime d’origine.

Une liste des tâches récentes d’un nœud de calcul reflète l’identifiant d’une tâche subordonnée si la tâche récente faisait partie d’une tâche multi-instances.

## <a name="obtain-information-about-subtasks"></a>Obtenir des informations sur les tâches subordonnées

Pour obtenir plus d’informations sur les tâches subordonnées à l’aide de la bibliothèque Batch.NET, appelez la méthode [CloudTask.ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks). Cette méthode renvoie des informations sur toutes les tâches subordonnées, ainsi que sur le nœud de calcul qui a exécuté les tâches. À partir de ces informations, vous pouvez déterminer le répertoire racine de chaque tâche subordonnée, l’identifiant du pool, son état actuel, le code de sortie, etc. Utilisez ces informations en même temps que la méthode [PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) pour obtenir les fichiers de la tâche subordonnée. Notez que cette méthode ne renvoie pas d’informations pour la tâche principale (identifiant 0).

> [!NOTE]
> Sauf indication contraire, les méthodes Batch.NET qui interviennent sur la tâche multi-instances [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) elle-même s’appliquent *uniquement* à la tâche principale. Par exemple, lorsque vous appelez la méthode [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) sur une tâche multi-instances, seuls les fichiers de la tâche principale sont renvoyés.

L’extrait de code suivant montre comment obtenir des informations sur les tâches subordonnées et comment demander le contenu du fichier à partir des nœuds sur lesquels elles sont exécutées.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Exemple de code

L’exemple de code [MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) sur GitHub montre comment une tâche multi-instances permet d’exécuter une application [MS-MPI](/message-passing-interface/microsoft-mpi) sur des nœuds de calcul Batch. Procédez comme suit pour exécuter l’exemple.

### <a name="preparation"></a>Préparation

1. Téléchargez le [Kit de développement logiciel (SDK) MS-MPI et les programmes d’installation de redistribution](/message-passing-interface/microsoft-mpi), puis installez-les. Après l’installation, vous pouvez vérifier que les variables d’environnement MS-MPI ont été définies.
1. Créez une version de *mise en production* de l’exemple de programme MPI [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld). Il s’agit du programme qui sera exécuté par la tâche multi-instances sur des nœuds de calcul.
1. Créez un fichier .zip contenant `MPIHelloWorld.exe` (que vous avez créé à l’étape 2) et `MSMpiSetup.exe` (que vous avez téléchargé à l’étape 1). À l’étape suivante, vous allez télécharger ce fichier .zip en tant que package d’application.
1. Utilisez le [Portail Azure](https://portal.azure.com) pour créer une [application](batch-application-packages.md) Batch appelée « MPIHelloWorld » et spécifiez le fichier .zip que vous avez créé à l’étape précédente en tant que version « 1.0 » du package d’application. Pour plus d’informations, consultez [Téléchargement et gestion des applications](batch-application-packages.md#upload-and-manage-applications).

> [!TIP]
> Créer une version de *mise en production* de `MPIHelloWorld.exe` assure que vous n’avez pas à inclure toutes les dépendances supplémentaires (par exemple `msvcp140d.dll` ou `vcruntime140d.dll`) dans votre package d’application.

### <a name="execution"></a>Exécution

1. Téléchargez le [fichier azure-batch-samples.zip](https://github.com/Azure/azure-batch-samples/archive/master.zip) à partir de GitHub.
1. Ouvrez la **solution** MultiInstanceTasks dans Visual Studio 2019. Le fichier de solution `MultiInstanceTasks.sln` se trouve à cet emplacement :

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Entrez vos informations d’identification de compte Batch et Stockage dans `AccountSettings.settings` dans le projet **Microsoft.Azure.Batch.Samples.Common**.
1. **Générez et exécutez** la solution MultiInstanceTasks pour exécuter l’exemple d’application MPI sur des nœuds de calcul dans un pool Batch.
1. *Facultatif* : Utilisez le [Portail Azure](https://portal.azure.com) ou [Batch Explorer](https://azure.github.io/BatchExplorer/) pour examiner les exemples de pool, de travail et de tâche (« MultiInstanceSamplePool », « MultiInstanceSampleJob », « MultiInstanceSampleTask ») avant de supprimer les ressources.

> [!TIP]
> Vous pouvez télécharger [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) gratuitement si vous n’avez pas encore Visual Studio.

Le résultat de `MultiInstanceTasks.exe` ressemble à ce qui suit :

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [prise en charge de MPI pour Linux sur Azure Batch](/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Découvrez comment [créer des pools de nœuds de calcul Linux](batch-linux-nodes.md) à utiliser dans vos solutions MPI Azure Batch.