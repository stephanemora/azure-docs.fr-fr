---
title: Démarrage rapide Azure - formation CNTK avec Batch AI - Python | Microsoft Docs
description: Apprenez rapidement à exécuter un travail de formation CNTK avec Batch AI à l’aide du kit de développement logiciel (SDK) Python
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 06/18/2018
ms.author: danlep
ms.openlocfilehash: 2ae0220b5240dc4a6e6d70056956140feb8153c4
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108525"
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Exécutez un travail de formation CNTK à l’aide du kit de développement logiciel (SDK) Python

Ce guide de démarrage rapide expose en détail comment utiliser le kit de développement logiciel (SDK) Python pour exécuter un travail de formation Microsoft Cognitive Toolkit (CNTK) à l’aide du service Batch AI.

Dans cet exemple, vous utilisez la base de données MNIST d’images dessinées à la main pour effectuer l’apprentissage d’un réseau de neurones convolutifs sur un cluster de GPU à nœud unique.

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Kit de développement logiciel (SDK) Azure Python - Consultez la page [instructions d’installation](/python/azure/python-sdk-azure-install) Cet article requiert au moins le package azure-mgmt-batchai version 2.0.0.

* Compte de stockage Azure - Consultez la page [Créer un compte de stockage Azure](../storage/common/storage-create-storage-account.md)

* Informations d’identification du principal de service Azure Active Directory- Consultez [Créer un principal du service avec Azure CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Inscrivez le fournisseur de ressources Batch AI une fois pour votre abonnement à l’aide d’Azure Cloud Shell ou d’Azure CLI. L’inscription d’un fournisseur peut prendre jusqu’à 15 minutes.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>Configurer les informations d’identification
Ajoutez le code suivant dans votre fichier de script et remplacez `FILL-IN-HERE` par les valeurs appropriées :

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'

# specify the location in which to create Batch AI resources
mylocation = 'eastus'
```

Notez qu’il n’est pas conseillé d’ajouter les informations d’identification dans le code source et que cela a été fait ici uniquement pour simplifier le guide de démarrage rapide.
Envisagez d’utiliser plutôt des variables d’environnement ou un autre fichier de configuration.

## <a name="create-batch-ai-client"></a>Créer un client Batch AI

Le code suivant crée un objet d’informations d’identification de principal du service et un client Batch AI :

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les clusters et les travaux Batch AI sont des ressources Azure et doivent être placés dans un groupe de ressources Azure. L’extrait de code suivant crée un groupe de ressources :

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': mylocation})
```


## <a name="prepare-azure-file-share"></a>Préparez le partage de fichiers Azure
À des fins d’illustration, ce guide de démarrage rapide utilise un partage de fichiers Azure pour héberger les scripts et les données de formation pour le travail de formation.

Créez un partage de fichiers nommé `batchaiquickstart`.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

Créer un répertoire dans le partage nommé `mnistcntksample`.

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False)
```
Téléchargez [l’exemple de package](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) et décompressez-le dans le répertoire actif. Le code suivant télécharge les fichiers requis dans le partage de fichiers Azure :

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-batch-ai-workspace"></a>Créer un espace de travail Batch AI

Un espace de travail est une collection de niveau supérieur de tous les types de ressources Batch AI. Vous créez votre cluster Batch AI et les essais dans un espace de travail.

```Python
workspace_name='myworkspace'
batchai_client.workspaces.create(resource_group_name, workspace_name, mylocation)
```

## <a name="create-gpu-cluster"></a>Créer le cluster GPU

Créez un cluster Batch AI. Dans cet exemple, le cluster se compose d’un seul nœud de machine virtuelle STANDARD_NC6. Cette taille de machine virtuelle comporte un GPU NVIDIA K80. Montez le partage de fichiers dans un dossier nommé `azurefileshare`. Le chemin d’accès complet de ce dossier sur le nœud de calcul GPU est `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # VM size. Use N-series for GPU
    vm_size='STANDARD_NC6',
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
        admin_user_name=admin_user_name,
        admin_user_password=admin_user_password),
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=1)
    ),
    # Configure each node in the cluster
    node_setup=models.NodeSetup(
        # Mount shared volumes to the host
        mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=storage_account_key),
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, workspace_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>Obtenir l’état du cluster

Surveillez l’état du cluster à l’aide de la commande suivante :

```Python
cluster = batchai_client.clusters.get(resource_group_name, workspace_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

Le code précédent imprime les informations d’allocation de cluster de base tel que dans l’exemple suivant :

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

Le cluster est prêt lorsque les nœuds sont alloués et ont terminé la préparation (consultez l’attribut `nodeStateCounts`). Si un problème est survenu, l’attribut `errors` contient la description de l’erreur.

## <a name="create-experiment-and-training-job"></a>Créer un essai et un travail de formation

Une fois que le cluster est créé, créez un essai (un conteneur logique pour un groupe de travaux connexes). Puis configurez et soumettez un travail d’apprentissage dans l’essai :

```Python
experiment_name='myexperiment'

batchai_client.experiments.create(resource_group_name, workspace_name, experiment_name)

job_name = 'myjob'

parameters = models.JobCreateParameters(
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Surveiller la tâche
Vous pouvez vérifier l’état du travail à l’aide du code suivant :

```Python
job = batchai_client.jobs.get(resource_group_name, workspace_name, experiment_name, job_name)

print('Job state: {0} '.format(job.execution_state))
```

Le résultat ressemble à ce qui suit : `Job state: running`.

Le `executionState` contient l’état actuel de l’exécution du travail :
* `queued` : le travail attend que les nœuds de cluster soient disponibles
* `running` : le travail est en cours d’exécution
* `succeeded` (ou `failed`) : le travail est terminé et `executionInfo` contient des détails sur le résultat

## <a name="list-stdout-and-stderr-output"></a>Lister le résultat stdout et stderr
Utilisez le code suivant pour répertorier les fichiers journaux, stdout et stderr générés :

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, workspace_name, experiment_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Répertorier les fichiers de modèle générés
Utilisez le code suivant pour répertorier les fichiers de modèle générés :
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, workspace_name, experiment_name,job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Supprimer des ressources

Utilisez le code suivant pour supprimer le travail :
```Python
batchai_client.jobs.delete(resource_group_name, workspace_name, experiment_name, job_name)
```

Utilisez le code suivant pour supprimer le cluster :
```Python
batchai_client.clusters.delete(resource_group_name, workspace_name, cluster_name)
```

Utilisez le code suivant pour supprimer toutes les ressources allouées :
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris comment exécuter un travail de formation CNTK sur un cluster Batch AI, à l’aide du kit de développement logiciel (SDK) Azure Python. Pour en savoir plus sur l’utilisation de Batch AI avec différents outils, consultez les [recettes de formation](https://github.com/Azure/BatchAI).
