---
title: Partage de fichiers Azure pour des pools Azure Batch | Microsoft Docs
description: Guide pratique pour monter un partage Azure Files à partir de nœuds de calcul dans un pool Linux ou Windows dans Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: 156dad25af5abd1b4d5db32569faf09a23fadfb1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022509"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Utiliser un partage de fichiers Azure avec un pool Batch

[Azure Files](../storage/files/storage-files-introduction.md) offre des partages de fichiers managés dans le cloud qui sont accessibles par le biais du protocole SMB (Server Message Block). Cet article fournit des informations et des exemples de code sur le montage et l’utilisation d’un partage de fichiers Azure sur des nœuds de calcul de pool. Les exemples de code utilisent les SDK Batch .NET et Python, mais vous pouvez effectuer des opérations similaires à l’aide d’autres outils et SDK Batch.

Batch fournit la prise en charge native des API pour l’utilisation d’objets blob de stockage Azure afin de lire et d’écrire des données. Toutefois, dans certains cas vous souhaiterez peut-être accéder à un partage de fichiers Azure à partir des nœuds de calcul de votre pool. Par exemple, vous avez une charge de travail héritée qui dépend d’un partage de fichiers SMB, ou vos tâches doivent accéder à des données partagées ou produire une sortie partagée. 

## <a name="considerations-for-use-with-batch"></a>Considérations relatives à l’utilisation de Batch

* Vous pouvez utiliser un partage de fichiers Azure quand vous avez des pools qui exécutent un nombre relativement faible de tâches en parallèle. Passez en revue les [objectifs de performance et d’extensibilité](../storage/files/storage-files-scale-targets.md) pour déterminer si Azure Files (qui utilise un compte Stockage Azure) doit être utilisé étant donné la taille attendue de votre pool et le nombre de fichiers d’actifs. 

* Les partages de fichiers Azure sont [rentables](https://azure.microsoft.com/pricing/details/storage/files/) et redondants à l’échelle mondiale car ils peuvent être configurés avec la réplication des données d’une autre région. 

* Vous pouvez monter un partage de fichiers Azure simultanément à partir d’un ordinateur local.

* Consultez également les [considérations de planification](../storage/files/storage-files-planning.md) générales pour les partages de fichiers Azure.


## <a name="create-a-file-share"></a>Créer un partage de fichiers

[Créez un partage de fichiers](../storage/files/storage-how-to-create-file-share.md) dans un compte de stockage lié à votre compte Batch, ou dans un compte de stockage distinct.

## <a name="mount-a-share-on-a-windows-pool"></a>Monter un partage sur un pool Windows

Cette section fournit des étapes et des exemples de code illustrant comment monter et utiliser un partage de fichiers Azure sur un pool de nœuds Windows. Pour plus d’informations, consultez la [documentation](../storage/files/storage-how-to-use-files-windows.md) relative au montage d’un partage de fichiers Azure sur Windows. 

Dans Batch, vous devez monter le partage chaque fois qu’une tâche est exécutée sur un nœud Windows. Actuellement, il n’est pas possible de conserver la connexion réseau entre les tâches sur les nœuds Windows.

Par exemple, incluez une commande `net use` pour monter le partage de fichiers dans le cadre de chaque ligne de commande de tâche. Pour monter le partage de fichiers, les informations d’identification suivantes sont nécessaires :

* **Nom d’utilisateur** : AZURE\\\<nom_compte_stockage\>, par exemple, AZURE\\*mystorageaccountname*
* **Mot de passe** : \<StorageAccountKeyWhichEnds in==>, par exemple, *XXXXXXXXXXXXXXXXXXXXX==*

La commande suivante monte un partage de fichiers *myfileshare* dans le compte de stockage *mystorageaccountname* en tant que le lecteur *S:*  :

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Par souci de simplicité, ces exemples transmettent les informations d’identification directement dans le texte. Dans la pratique, nous vous recommandons fortement de gérer les informations d’identification à l’aide de variables d’environnement, de certificats ou d’une solution telle qu’Azure Key Vault.

Pour simplifier l’opération de montage, si vous le souhaitez vous pouvez conserver les informations d’identification sur les nœuds. Ensuite, vous pouvez monter le partage sans informations d’identification. Effectuez les deux étapes suivantes :

1. Exécutez l’utilitaire en ligne de commande `cmdkey` à l’aide d’une tâche de démarrage dans la configuration du pool. Cela rend persistantes les informations d’identification sur chaque nœud Windows. La ligne de commande de tâche de démarrage ressemble à ceci :

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Montez le partage sur chaque nœud dans le cadre de chaque tâche à l’aide de `net use`. Par exemple, la ligne de commande de tâche suivante monte le partage de fichiers en tant que lecteur *S:* . Elle serait suivie d’une commande ou d’un script qui référence le partage. Des informations d’identification mises en cache sont utilisées dans l’appel à `net use`. Cette étape part du principe que vous utilisez pour les tâches la même identité d’utilisateur que celle utilisée dans la tâche de démarrage sur le pool, ce qui ne convient pas à tous les scénarios.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Exemple en code C#
L’exemple de code C# suivant montre comment conserver les informations d’identification sur un pool Windows à l’aide d’une tâche de démarrage. Le nom du service de fichiers de stockage et les informations d’identification de stockage sont passés en tant que constantes définies. Ici, la tâche de démarrage s’exécute sous un compte d’auto-utilisateur standard (non-administrateur) avec le pool comme étendue.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Après avoir stocké les informations d’identification, utilisez vos lignes de commande de tâche pour monter et référencer le partage dans les opérations de lecture ou d’écriture. En guise d’exemple de base, la ligne de commande de tâche dans l’extrait de code suivant utilise la commande `dir` pour répertorier les fichiers dans le partage de fichiers. Veillez à exécuter chaque tâche à l’aide de la même [identité d’utilisateur](batch-user-accounts.md) que celle utilisée pour exécuter la tâche de démarrage dans le pool. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Monter un partage sur un pool Linux

Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l’aide du [client noyau CIFS](https://wiki.samba.org/index.php/LinuxCIFS). L’exemple suivant montre comment monter un partage de fichiers sur un pool de nœuds de calcul Ubuntu 16.04 LTS. Si vous utilisez une autre distribution Linux, les étapes générales sont similaires, mais vous devez utiliser le gestionnaire de package approprié pour la distribution. Pour plus d’informations et pour obtenir des exemples supplémentaires, consultez [Utiliser Azure Files avec Linux](../storage/files/storage-how-to-use-files-linux.md).

Tout d’abord, sous une identité d’utilisateur administrateur, installez le package `cifs-utils` et créez le point de montage (par exemple */mnt/MyAzureFileShare*) dans le système de fichiers local. Vous pouvez créer un dossier pour un point de montage n’importe où sur le système de fichiers, mais il est d’usage courant de le créer sous le dossier `/mnt`. Veillez à ne pas créer de point de montage directement dans `/mnt` (sur Ubuntu) ou `/mnt/resource` (sur les autres distributions).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Ensuite, exécutez la commande `mount` pour monter le partage de fichiers, en fournissant ces informations d’identification :

* **Nom d’utilisateur** : \<nom_compte_stockage\>, par exemple, *mystorageaccountname*
* **Mot de passe** : \<StorageAccountKeyWhichEnds in==>, par exemple, *XXXXXXXXXXXXXXXXXXXXX==*

La commande suivante monte un partage de fichiers *myfileshare* dans le compte de stockage *mystorageaccountname* dans */mnt/MyAzureFileShare* : 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Par souci de simplicité, ces exemples transmettent les informations d’identification directement dans le texte. Dans la pratique, nous vous recommandons fortement de gérer les informations d’identification à l’aide de variables d’environnement, de certificats ou d’une solution telle qu’Azure Key Vault.

Sur un pool Linux, vous pouvez combiner toutes ces étapes dans une tâche de démarrage unique, ou les exécuter dans un script. Exécutez la tâche de démarrage en tant qu’administrateur sur le pool. Configurez votre tâche de démarrage pour qu’elle attende de se terminer correctement avant d’exécuter sur le pool des tâches supplémentaires qui référencent le partage.

### <a name="python-example"></a>Exemple Python

L’exemple Python suivant montre comment configurer un pool Ubuntu pour monter le partage dans une tâche de démarrage. Le point de montage, le point de terminaison de partage de fichiers et les informations d’identification de stockage sont passés en tant que constantes définies. La tâche de démarrage s’exécute sous un compte d’auto-utilisateur administrateur avec le pool comme étendue.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Après avoir monté le partage et défini un travail, utilisez le partage dans vos lignes de commande de tâche. Par exemple, la commande de base suivante utilise `ls` pour répertorier les fichiers dans le partage de fichiers.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir d’autres options permettant de lire et d’écrire des données dans Batch, consultez la [vue d’ensemble des fonctionnalités de Batch](batch-api-basics.md) et l’article [Conserver les résultats des tâches et des travaux](batch-task-output.md).

* Consultez également la boîte à outils [Batch Shipyard](https://github.com/Azure/batch-shipyard), qui contient des [recettes Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) afin de déployer des systèmes de fichiers pour des charges de travail de conteneur Batch.
