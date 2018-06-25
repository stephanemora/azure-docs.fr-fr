---
title: Charges de travail de conteneur sur Azure Batch | Microsoft Docs
description: Découvrez comment exécuter des applications à partir d’images conteneur sur Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 06/04/2018
ms.author: danlep
ms.openlocfilehash: 4ee8425bb5c3830b029b766aad464df0ffb15f41
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801113"
---
# <a name="run-container-applications-on-azure-batch"></a>Exécuter des applications de conteneur sur Azure Batch

Azure Batch vous permet d’exécuter et de mettre à l’échelle de nombreux travaux informatiques par lots sur Azure. Les tâches Batch peuvent être exécutées directement sur des machines virtuelles (nœuds) dans un pool Batch, mais vous pouvez également configurer un pool Batch pour exécuter des tâches dans des conteneurs compatibles Docker sur les nœuds. Cet article vous montre comment créer un pool de nœuds de calcul prenant en charge les tâches en cours d’exécution du conteneur, puis exécuter des tâches du conteneur dans le pool. 

Vous devez être familiarisé avec les concepts de conteneur et savoir comment créer un pool et une tâche Batch. Les exemples de code utilisent les Kits de développement logiciel (SDK) .NET Batch et Python. Vous pouvez également utiliser d’autres kits de développement logiciel (SDK) et outils Batch, notamment le portail Azure, pour créer des pools Batch pour conteneur et pour exécuter des tâches du conteneur.

## <a name="why-use-containers"></a>Pourquoi utiliser des conteneurs ?

L’utilisation de conteneurs permet de lancer simplement des tâches par lot sans avoir à gérer un environnement et des dépendances pour exécuter des applications. Les conteneurs déploient les applications sous la forme d’unités légères, portables et autonomes pouvant s’exécuter dans différents environnements. Par exemple, vous pouvez générer et tester localement un conteneur de test, puis télécharger l’image conteneur vers un registre dans Azure ou ailleurs. Le modèle de déploiement de conteneur permet de s’assurer que l’environnement d’exécution de votre application est toujours correctement installé et configuré, où que votre application soit hébergée. Les tâches basées sur le conteneur dans Batch peuvent également tirer parti des fonctionnalités des tâches non basées sur le conteneur, notamment les packages d’applications et la gestion des fichiers de ressources et des fichiers de sortie. 

## <a name="prerequisites"></a>Prérequis

* **Versions du SDK** : le SDK prend en charge des images conteneur dans les versions suivantes :
    * API REST (version : 6.0 du 01/09/2017)
    * Kit de développement logiciel Batch .NET SDK (version 8.0.0)
    * Kit de développement logiciel Batch Python (version 4.0)
    * Kit de développement logiciel Batch Java (version 3.0)
    * Kit de développement logiciel Batch Node.js (version 3.0)

* **Comptes** : sur votre abonnement Azure, vous devez créer un compte Batch et, éventuellement, un compte de stockage Azure.

* **Une image de machine virtuelle prise en charge** : les conteneurs sont uniquement pris en charge dans les pools créés lors de la configuration des machines virtuelles à partir d’images détaillées dans la section « Images de machines virtuelles prises en charge ». Si vous fournissez une image personnalisée, consultez les considérations présentées dans la section suivante et la configuration requise dans [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md). 

### <a name="limitations"></a>Limites

* Batch prend en charge RDMA uniquement pour les conteneurs exécutés sur les pools Linux.

## <a name="supported-virtual-machine-images"></a>Images de machines virtuelles prises en charge

Utilisez une des images Windows ou Linux prises en charge suivantes afin de créer un pool de nœuds de calcul de machines virtuelles pour les charges de travail du conteneur. Pour plus d’informations sur les images de la Place de marché qui sont compatibles avec Batch, consultez la [liste des images de machine virtuelle](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Images Windows

Pour les charges de travail de conteneur Windows, Batch prend actuellement en charge l’image **Windows Server 2016 Datacenter avec des conteneurs** dans la Place de marché Azure. Seules les images de conteneur Docker sont prises en charge sur Windows.

Vous pouvez également créer des images personnalisées à partir de machines virtuelles exécutant Docker sur Windows.

### <a name="linux-images"></a>Images Linux

Pour les charges de travail de conteneur Linux, Batch prend actuellement en charge les images Linux suivantes publiées par Microsoft Azure Batch dans la Place de marché Azure :

* **CentOS pour les pools de conteneur Azure Batch**

* **CentOS (avec pilotes RDMA) pour les pools de conteneur Azure Batch**

* **Serveur Ubuntu pour les pools de conteneur Azure Batch**

* **Serveur Ubuntu (avec pilotes RDMA) pour les pools de conteneur Azure Batch**

Ces images sont uniquement prises en charge pour une utilisation dans des pools Azure Batch. Elles offrent :

* Une version d’exécution de conteneur [Moby](https://github.com/moby/moby) préinstallée 

* Des pilotes NVIDIA GPU préinstallés pour simplifier le déploiement sur des machines virtuelles Azure N-series

* Images avec ou sans pilotes RDMA préinstallés ; ces pilotes permettent aux nœuds de pool d’accéder au réseau RDMA Azure lors du déploiement sur des tailles de machine virtuelle prenant en charge RDMA  

Vous pouvez également créer des images personnalisées à partir de machines virtuelles exécutant Docker sur l’une des distributions Linux compatibles avec Batch. Si vous souhaitez fournir votre propre image Linux personnalisée, consultez les instructions dans [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md).

Pour la prise en charge de Docker sur une image personnalisée, installez [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Remarques supplémentaires relatives à l’utilisation d’une image personnalisée Linux :

* Pour tirer parti des performances du GPU de tailles Azure N-series lors de l’utilisation d’une image personnalisée, préinstallez des pilotes NVIDIA. En outre, vous devez installer l’utilitaire moteur Docker pour GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Pour accéder au réseau RDMA Azure, utilisez une taille de machine virtuelle prenant en charge RDMA. Les pilotes RDMA nécessaires sont installés dans les images CentOS HPC et Ubuntu prises en charge par Batch. Une configuration supplémentaire peut être nécessaire pour exécuter des charges de travail MPI. Voir [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Configuration du conteneur pour le pool Batch

Pour permettre à un pool Batch d’exécuter des charges de travail sur le conteneur, vous devez spécifier les paramètres [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) dans l’objet [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) du pool. (Cet article fournit des liens vers la référence sur l’API .NET Batch. Les paramètres correspondants sont dans l’API [Python Batch](/python/api/azure.batch).)

Vous pouvez créer un pool pour conteneurs avec ou sans images conteneur prérécupérées, comme indiqué dans les exemples suivants. Le processus d’extraction (ou prérécupération) vous permet de précharger les images conteneur à partir du Hub Docker ou d’un autre registre de conteneurs sur Internet. Pour de meilleures performances, utilisez un [Registre de conteneurs Azure](../container-registry/container-registry-intro.md) dans la même région que le compte Batch.

L’avantage de la prérécupération d’images conteneur est que, lors de la première exécution des tâches, ces dernières ne doivent pas attendre que l’image conteneur soit téléchargée. La configuration du conteneur extrait des images conteneur vers les machines virtuelles une fois le pool créé. Les tâches exécutées sur le pool peuvent ensuite référencer la liste des images et des options d’exécution du conteneur.


### <a name="pool-without-prefetched-container-images"></a>Pool sans images conteneur prérécupérées

Pour configurer un pool pour conteneurs sans images conteneur prérécupérées, définissez des objets `ContainerConfiguration` et `VirtualMachineConfiguration`, comme indiqué dans l’exemple Python suivant. Cet exemple utilise le serveur Ubuntu pour l’image de pools de conteneur Azure Batch dans la Place de marché.


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Prérécupérer des images pour la configuration du conteneur

Pour prérécupérer des images conteneur sur le pool, ajoutez la liste d’images conteneur (`container_image_names` dans Python) à `ContainerConfiguration`. 

L’exemple de base Python suivant montre comment prérécupérer une image conteneur Ubuntu standard à partir du [Hub Docker](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


L’exemple C# suivant suppose que vous souhaitez prérécupérer une image TensorFlow sur le [Hub Docker](https://hub.docker.com). Cet exemple inclut une tâche de démarrage qui s’exécute dans l’hôte de la machine virtuelle sur les nœuds du pool. Vous pouvez lancer une tâche de démarrage dans l’hôte, par exemple, pour monter un serveur de fichiers accessible à partir des conteneurs.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prérécupérer des images à partir d’un registre de conteneurs privé

Vous pouvez également prérécupérer des images conteneur en vous authentifiant sur un serveur de registres de conteneurs privé. Dans l’exemple suivant, les objets `ContainerConfiguration` et `VirtualMachineConfiguration` prérécupèrent une image TensorFlow privée dans un registre de conteneurs Azure privé. La référence d’image est la même que dans l’exemple précédent.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```


## <a name="container-settings-for-the-task"></a>Paramètres de conteneur pour la tâche

Pour exécuter des tâches de conteneur sur les nœuds de calcul, vous devez spécifier les paramètres spécifiques aux conteneurs, tels que des options d’exécution des tâches, des images à utiliser et un registre.

Utilisez la propriété `ContainerSettings` des classes de tâches pour configurer les paramètres spécifiques au conteneur. Ces paramètres sont définis par la classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

Si vous exécutez des tâches sur des images conteneur, la [tâche de cloud](/dotnet/api/microsoft.azure.batch.cloudtask) et la [tâche du gestionnaire de travaux](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) nécessitent des paramètres de conteneur. Toutefois, la [tâche de démarrage](/dotnet/api/microsoft.azure.batch.starttask), la [tâche de préparation du travail](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) et la [tâche de mise en production du travail](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) ne nécessitent pas de paramètres de conteneur (autrement dit, elles peuvent s’exécuter dans un contexte de conteneur ou directement sur le nœud).

Lorsque vous configurez les paramètres de conteneur, tous les répertoires récursifs sous le `AZ_BATCH_NODE_ROOT_DIR` (la racine des répertoires Azure Batch sur le nœud) et toutes les variables d’environnement des tâches sont mappes dans le conteneur, et la ligne de commande des tâches est exécutée dans le conteneur.

L’extrait de code Python suivant montre une ligne de commande de base exécutée dans un conteneur Ubuntu extrait du Hub Docker. Les options d’exécution du conteneur sont des arguments supplémentaires pour la commande `docker create` exécutée par la tâche. Ici, l’option `--rm` supprime le conteneur une fois que la tâche est terminée.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='echo hello',
    container_settings=task_container_settings
)

```

L’exemple C# suivant montre les paramètres de conteneur de base d’une tâche de cloud :

```csharp
// Simple container task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Étapes suivantes

* Consultez également la boîte à outils [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour faciliter le déploiement de charges de travail de conteneur sur Azure Batch par le biais de [recettes Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Pour plus d’informations sur l’installation et l’utilisation de Docker CE sur Linux, consultez la documentation [Docker](https://docs.docker.com/engine/installation/).

* Pour en savoir plus sur l’utilisation d’images personnalisées, voir [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md).

* En savoir plus sur le [projet Moby](https://mobyproject.org/), une infrastructure pour la création de systèmes basés sur le conteneur.