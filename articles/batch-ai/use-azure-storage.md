---
title: Stockage d’entrée et de sortie de travail Batch AI avec Stockage Azure | Microsoft Docs
description: Comment utiliser Stockage Azure avec Batch AI pour le stockage cloud simple et rapide de fichiers d’entrée et de sortie
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: c1ebcae04b51fbf18da22872e3e4160a37a7e2fe
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201157"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Stocker l’entrée et la sortie de travail Batch AI avec Stockage Azure

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ce guide décrit comment utiliser Stockage Azure pour stocker les fichiers d’entrée et de sortie lors de l’exécution d’un travail. Stockage Azure est une des diverses options de stockage prises en charge par Batch AI. Batch AI s’intègre avec Stockage Azure en montant des systèmes Stockage Azure sur un système de fichiers de travail ou cluster Batch AI, permettant ainsi d’accéder sans problème aux fichiers stockés dans le cloud. 

## <a name="introduction-to-azure-storage"></a>Présentation de Stockage Azure

Le Stockage Azure est une solution de stockage cloud de Microsoft. Batch AI prend en charge le montage de conteneurs d’objets blob Azure et de partages de fichiers Azure sur des travaux ou clusters Batch AI, permettant ainsi d’accéder aux fichiers à partir d’un travail comme s’ils se trouvaient dans le système de fichiers natif. Batch AI monte des conteneurs d’objets blob Azure avec [blobfuse](https://github.com/Azure/azure-storage-fuse) et des partages de fichiers Azure via le protocole SMB. Pour plus d’informations sur le Stockage Azure, consultez la page [Présentation de Stockage Azure](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Stocker des jeux de données et des scripts d’entrée dans Stockage Azure

Lorsque vous choisissez Stockage Azure pour votre environnement Batch AI, nous vous recommandons de stocker vos fichiers d’entrée (comme des jeux de données) dans un conteneur d’objets blob, qui a un débit plus élevé, et de stocker votre sortie de formation dans un partage de fichiers, qui prend en charge la diffusion en continu (permettant de lire les journaux de sortie pendant l’exécution simultanée du travail). 

Avant de pouvoir utiliser Stockage Azure, vous devez [créer un compte de stockage Azure](../storage/common/storage-quickstart-create-account.md). Batch AI prend en charge le montage de volumes à partir de comptes de Stockage Azure v1 à usage général (GPv1) et v2 à usage général (GPv2). Le compte de Stockage Azure peut contenir plusieurs conteneurs d’objets blob ou instances de partage de fichiers. Prenez en compte vos exigences en termes de coûts et de performances lorsque vous choisissez le type de compte de stockage à créer. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../storage/common/storage-account-overview.md). 

Pour créer un conteneur d’objets blob et pour charger votre jeu de données dans un conteneur d’objets blob Azure, choisissez l’une des méthodes suivantes :
- [Portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md) pour charger avec une interface graphique utilisateur basée sur le web. Pour charger un petit nombre de fichiers, le portail Azure fournit l’opération la plus simple.
- [Interface CLI de Stockage Azure](../storage/blobs/storage-quickstart-blobs-cli.md) pour charger via la ligne de commande (prend en charge le chargement de répertoire). Pour charger des répertoires de fichiers, utilisez `az storage blob upload-batch`.
- [Autres techniques](../storage/common/storage-moving-data.md), notamment l’utilisation de Kits de développement logiciel (SDK) d’application.

De même, pour créer un partage de fichiers Azure, choisissez l’une des méthodes suivantes :
- [Portail Azure](../storage/files/storage-how-to-use-files-portal.md)
- [Interface CLI de Stockage Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Autres techniques](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Stockage automatique avec Batch AI

Vous pouvez également créer un compte de Stockage Azure avec un partage de fichiers Azure et un conteneur d’objets blob (et monter automatiquement ces volumes sur un cluster Batch AI) à l’aide du paramètre `--use-auto-storage` avec `az batchai cluster create`. Vous pourrez trouver plus d’informations [ici](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Monter Stockage Azure 

### <a name="mount-volumes-to-a-job"></a>Monter des volumes sur un travail

Le montage d’un volume Stockage Azure permet d’y accéder via le système de fichiers créé pour chaque travail. Un travail peut donc lire et écrire sans problème des fichiers sur le stockage cloud comme s’il s’agissait de fichiers locaux.

Pour monter un volume Stockage Azure sur un travail créé avec Azure CLI, utilisez la propriété `mountVolumes` dans votre fichier `job.json` lors de l’exécution de `az batchai job create`. Pour obtenir un exemple, consultez [Tensorflow GPU Distributed Recipe](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json) (Recette distribuée du GPU TensorFlow). Le schéma pour `mountVolumes` est :
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` et `azureBlobFileSystems` sont des tableaux d’objets qui représentent les volumes à monter. Descriptions des espaces réservés :

- < RELATIVE_MOUNT_PATH > : le volume est monté sur ce chemin d’accès. Par exemple, si `relativeMountPath` est `jobs`, le volume se trouve dans `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`)
- <STORAGE_ACCOUNT_NAME> : le nom du compte de Stockage Azure qui contient le partage de fichiers ou le conteneur d’objets blob
- < FILE_SHARE_NAME > : le nom du partage de fichiers
- <BLOB_CONTAINER_NAME> : le nom du conteneur d’objets blob

Pour monter des volumes Stockage Azure avec les Kits de développement logiciel (SDK) Azure Batch AI, définissez la propriété `mount_volumes` (Python) ou `MountVolumes` (C#, Java) sur `JobCreateParameters`. Vous devez fournir les informations d’identification du compte de stockage lors du montage de volumes avec des Kits de développement logiciel (SDK) Azure Batch AI. Consultez les schémas de montage de volumes dans [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet) et [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai.mountvolumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>Monter des volumes sur un cluster

Batch AI prend également en charge le montage de volumes Stockage Azure sur un cluster Batch AI. Lorsqu’un volume est monté sur un cluster, tous les travaux en cours d’exécution sur ce cluster peuvent utiliser des volumes montés sur ce cluster. Alors que le montage au niveau du travail offre une flexibilité optimale (autorisant différents volumes montés sur chaque travail), le montage au niveau du cluster peut suffire dans des scénarios simples.

Pour monter un volume Stockage Azure sur un cluster créé avec Azure CLI, utilisez la propriété `mountVolumes` dans votre fichier `cluster.json` lors de l’exécution de `az batchai cluster create`. Le schéma pour `mountVolumes` lors du montage d’un cluster est le même que lors du montage sur un travail. 

Vous pouvez également utiliser la propriété `mount_volumes` (Python) ou `MountVolumes` (C#, Java) sur `ClusterCreateParameters` lors du montage avec les Kits de développement logiciel (SDK) Azure Batch AI. 

## <a name="access-mounted-filesystem-in-a-job"></a>Accéder au système de fichiers monté sur un travail

### <a name="azbatchaijobmountroot-environment-variable"></a>Variable d’environnement $AZ_BATCHAI_JOB_MOUNT_ROOT

Dans l’environnement d’exécution du travail, le répertoire contenant les systèmes de stockage montés est accessible avec la variable d’environnement `$AZ_BATCHAI_JOB_MOUNT_ROOT` (si vous utilisez le montage au niveau du travail). Si vous utilisez le montage au niveau du cluster, cette variable d’environnement est `$AZ_BATCHAI_MOUNT_ROOT`. Les exemples suivants supposent que vous utilisez le montage au niveau du travail.

Pour fournir le chemin d’accès aux données d’un volume monté, utilisez la variable d’environnement `$AZ_BATCHAI_JOB_MOUNT_ROOT` ainsi que le chemin d’accès monté. Par exemple, si le script de formation `train.py` a été chargé dans un partage de fichiers Azure monté sur le chemin d’accès de montage relatif `scripts`, le fichier est disponible dans `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Si votre script de formation nécessite de connaître un chemin d’accès, vous devez le transmettre comme un argument de ligne de commande. Par exemple, si vous avez stocké vos données dans un dossier appelé `train_data` dans un conteneur d’objets blob Azure monté sur le chemin d’accès `data`, vous pouvez transmettre `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` comme un argument de ligne de commande à votre script. Vous devez donc modifier votre script pour qu’il accepte les arguments de ligne de commande.

### <a name="abbreviate-input-paths"></a>Abréger les chemins d’accès d’entrée

Pour abréger les chemins d’accès d’entrée en tant que variable d’environnement, utilisez la propriété `inputDirectories` de votre fichier `job.json` (ou `models.JobCreateParameters.input_directories` si vous utilisez le Kit de développement logiciel (SDK) Batch AI). Le schéma de `inputDirectories` est :

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Chaque chemin d’accès spécifié est placé dans une variable d’environnement appelée `$AZ_BATCHAI_INPUT_<ID>`. L’utilisation de cette méthode peut simplifier les chemins d’accès aux fichiers d’entrée ou répertoires. Par exemple, pour abréger le chemin d’accès à un script de formation : si `"id"` est `"SCRIPT"` et `"path"` est `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, ce chemin d’accès est disponible dans `$AZ_BATCHAI_INPUT_SCRIPT` dans l’environnement d’exécution du travail.

Vous pourrez trouver plus d’informations [ici](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Abréger les chemins d’accès de sortie

Pour abréger les chemins d’accès de sortie en tant que variable d’environnement, utilisez la propriété `outputDirectories` de votre fichier `job.json` (ou `models.JobCreateParameters.output_directories` si vous utilisez le Kit de développement logiciel (SDK) Batch AI). L’utilisation de cette méthode peut simplifier les chemins d’accès aux fichiers de sortie. Le schéma de `outputDirectories` est :

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Chaque chemin d’accès spécifié est placé dans une variable d’environnement appelée `$AZ_BATCHAI_OUTPUT_<ID>`. `pathPrefix` détermine le volume monté pour stocker la sortie (par exemple, `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`). `pathSuffix` détermine le nom du dossier de la sortie (par exemple, `"logs"`, `"checkpoints"`). Le chemin d’accès réel de la sortie est une concaténation de `pathPrefix`, `jobOutputDirectoryPathSegment` (généré automatiquement pour chaque travail) et `pathSuffix`.

Vous pourrez trouver plus d’informations [ici](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Récupérer la sortie du travail de Stockage Azure

### <a name="use-azure-portal"></a>Utiliser le portail Azure

Le portail Azure est une méthode pratique pour afficher la sortie des travaux à l’aide d’un Explorateur de fichiers de l’interface graphique utilisateur. Toutefois, si vous souhaitez afficher la sortie à partir de Stdout et Stderr, ou à partir d’un chemin d’accès dans `outputDirectories`, les fichiers sont placés dans un chemin d’accès généré automatiquement dans votre volume Stockage Azure. Pour plus d’informations, voir plus bas.

### <a name="access-stdout-and-stderr-output"></a>Accéder à la sortie Stdout et Stderr

Utilisez la propriété `stdOutErrPathPrefix` de `job.json` pour indiquer au travail où placer les journaux d’exécution du travail et la sortie Stdout et Stderr. Par exemple, si vous avez monté un partage de fichiers sur le chemin d’accès de montage relatif `outputs` et que vous définissez `stdOutErrPathPrefix` sur `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`, la sortie du travail Stdout et Stderr est disponible dans `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` dans ce volume monté. Ce chemin d’accès généré automatiquement est utilisé pour empêcher les collisions de sortie entre les travaux portant le même nom.

Vous pourrez trouver plus d’informations [ici](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>Répertorier les fichiers de sortie

Vous pouvez utiliser Azure CLI pour répertorier les fichiers de sortie disponibles d’un travail avec la commande `az batchai job file list`. Par exemple, pour répertorier les fichiers dans le répertoire de sortie standard d’un travail, utilisez `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

Pour plus d’informations et d’exemples, consultez [ici](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Diffuser en continu les fichiers de sortie

Vous pouvez utiliser Azure CLI pour diffuser en continu les fichiers avec la commande `az batchai job file stream`. Par exemple, pour afficher :
- Stdout : `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr : `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

Pour plus d’informations et d’exemples, consultez [ici](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les commandes CLI d’interaction avec Stockage Azure, consultez la [documentation de la CLI Azure Batch AI](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Pour d’autres exemples d’utilisation de Batch AI, notamment le montage du stockage et la lecture de fichiers de sortie, consultez les [recettes de Jupyter Notebook pour Batch AI](https://github.com/Azure/BatchAI).
- Découvrez d’autres options de montage du stockage, notamment le [montage d’un serveur NFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) et le [montage de votre propre cluster NFS, cifs ou GlusterFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)