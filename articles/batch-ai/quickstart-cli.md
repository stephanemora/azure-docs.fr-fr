---
title: Démarrage rapide Azure - Formation CNTK avec Batch AI - Azure CLI | Microsoft Docs
description: Apprenez rapidement à exécuter un travail de formation CNTK avec Batch AI à l’aide de l’interface Azure CLI
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 8d2381f710e87751bd6547c7f435080f185020d5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608543"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Exécutez un travail de formation CNTK à l’aide d’Azure CLI

Azure CLI 2.0 vous permet de créer et de gérer des ressources Batch AI, créer/supprimer des serveurs de fichiers et des clusters Batch AI, et de soumettre, d’arrêter, de supprimer et de surveiller des travaux d’apprentissage.

Ce démarrage rapide montre comment créer un cluster GPU et exécuter un travail d’apprentissage à l’aide de Microsoft Cognitive Toolkit.

Le script de formation [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) est disponible sur la page GitHub de Batch AI. Ce script effectue l’apprentissage d’un réseau de neurones convolutifs sur une base de données MNIST de chiffres manuscrits.

L’exemple CNTK officiel a été modifié pour accepter l’emplacement du jeu de données d’apprentissage et l’emplacement du répertoire de sortie via des arguments de ligne de commande.

## <a name="quickstart-overview"></a>Vue d’ensemble du démarrage rapide

* Créer un cluster GPU à nœud unique (avec une taille de machine virtuelle `Standard_NC6`) avec le nom `nc6` ;
* Créer un nouveau compte de stockage pour stocker l’entrée et la sortie du travail ;
* Créer un partage de fichiers Azure avec deux dossiers `logs` et `scripts` pour stocker les scripts d’apprentissage et les sorties des travaux ;
* Créer un conteneur d’objets Blob Azure `data` pour stocker les données d’apprentissage ;
* Déployer le script de formation et les données d’apprentissage pour le partage de fichiers créé et le conteneur ;
* Configurer le travail pour monter le partage de fichiers Azure et un conteneur d’objets blob Azure sur le nœud de cluster et les rendre disponibles en tant que système de fichiers normal à `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts`, et `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` est une variable d’environnement définie par Batch AI pour le travail.
* Surveiller l’exécution du travail par la diffusion en continu de sa sortie standard ;
* Après l’achèvement du travail, vérifier sa sortie et les modèles générés ;
* À la fin, supprimer toutes les ressources attribuées.

# <a name="prerequisites"></a>Prérequis

* Abonnement Azure : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Accès à Azure CLI 2.0 avec une version 2.0.31 ou ultérieure. Vous pouvez utiliser Azure CLI 2.0 disponible dans [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) ou l’installer localement en suivant [ces instructions](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

# <a name="cloud-shell-only"></a>Cloud Shell uniquement

Si vous utilisez Cloud Shell, modifiez le répertoire de travail par `/usr/$USER/clouddrive`, car votre répertoire de base ne possède pas d’espace vide :

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>Création d’un groupe de ressources

Un groupe de ressources Azure est un conteneur logique pour le déploiement et la gestion de ressources Azure. La commande suivante crée un nouveau groupe de ressources ```batchai.quickstart``` dans l’emplacement de l’est des États-Unis :

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>Créer le cluster GPU

La commande suivante crée un cluster GPU à nœud unique (la taille de la machine virtuelle est Standard_NC6) avec Ubuntu DSVM comme image du système d’exploitation :

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Ubuntu DSVM vous permet d’exécuter des travaux d’apprentissage dans des conteneurs docker et d’exécuter les infrastructures de formation approfondies les plus populaires directement sur la machine virtuelle.

L’option `--generate-ssh-keys` indique à Azure CLI pour générer des clés SSH privées et publiques si vous ne les avez pas déjà. Vous pouvez accéder aux nœuds de cluster à l’aide du nom d’utilisateur actuel et de la clé SSH générée.

Notez que, si vous utilisez Cloud Shell, sauvegardez le dossier ~/.ssh vers un stockage permanent.

Exemple de sortie :
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "alex",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

# <a name="create-a-storage-account"></a>Créer un compte de stockage

La commande suivante crée un nouveau compte de stockage dans la même région que le groupe de ressources batchai.repices. Mettez à jour la commande avec un nom de compte de stockage unique.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

Si le nom de compte de stockage sélectionné n’est pas disponible, la commande ci-dessus signale l’erreur correspondante. Dans ce cas, choisissez un autre nom et réessayez.

# <a name="data-deployment"></a>Déploiement de données

## <a name="download-the-training-script-and-training-data"></a>Télécharger le script de formation et les données d’apprentissage

* Téléchargez et décompressez la base de données MNIST prétraitée à partir de [cet emplacement](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) dans le dossier actuel.

Pour GNU/Linux ou Cloud Shell :

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Notez que vous devrez peut-être installer `unzip` si votre distribution GNU/Linux ne le possède pas.

* Téléchargez l’exemple de script [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) dans le dossier actuel :

Pour GNU/Linux ou Cloud Shell :

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Créer un partage de fichiers Azure et déployer le script de formation

Les commandes suivantes créent des partages de fichiers Azure `scripts` et `logs` puis copient le script de formation dans le dossier `cntk` à l’intérieur du partage `scripts` :

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>Créer un conteneur d’objets Blob et déployer des données d’apprentissage

Les commandes suivantes créent le conteneur d’objets Blob Azure `data` et copient les données d’apprentissage dans le dossier `mnist_cntk` :
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>Soumettre un travail de formation

## <a name="prepare-job-configuration-file"></a>Préparer un fichier de configuration du travail

Créer un fichier de configuration du travail d’apprentissage `job.json` avec le contenu suivant :
```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Ce fichier de configuration spécifie :

* `nodeCount` -le nombre de nœuds requis pour le travail (1 pour ce démarrage rapide) ;
* `cntkSettings` -spécifie le chemin d’accès du script de formation et les arguments de ligne de commande. Les arguments de ligne de commande incluent le chemin d’accès vers les données d’apprentissage et le chemin d’accès de destination pour le stockage des modèles générés. `AZ_BATCHAI_OUTPUT_MODEL` est une variable d’environnement définie par Batch AI basée sur la configuration du répertoire de sortie (voir ci-dessous) ;
* `stdOutErrPathPrefix` -chemin d’accès où Batch AI créera des répertoires contenant la sortie et les journaux du travail ;
* `outputDirectories` -collection des répertoires de sortie à créer par Batch AI. Pour chaque répertoire, Batch AI crée une variable d’environnement avec le nom `AZ_BATCHAI_OUTPUT_<id>`, où `<id>` est l’identificateur du répertoire ;
* `mountVolumes` -liste des systèmes de fichiers à monter pendant l’exécution du travail. Les systèmes de fichiers sont montés sous `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` est une variable d’environnement définie par Batch AI ;
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` indique que le nom de compte de stockage sera spécifié lors de l’envoi du travail via le paramètre --storage-account-name ou la variable d’environnement `AZURE_BATCHAI_STORAGE_ACCOUNT` sur votre ordinateur.

## <a name="submit-the-job"></a>Envoyer le travail

Utilisez la commande suivante pour soumettre le travail au cluster :

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Exemple de sortie :
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>Surveiller l’exécution du travail

Le script de formation signale la progression de la formation dans le fichier `stderr.txt` du répertoire de sortie standard. Vous pouvez surveiller la progression à l’aide de la commande suivante :

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Exemple de sortie :
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

La diffusion en continu est arrêtée une fois le travail terminé (réussite ou échec).

# <a name="inspect-generated-model-files"></a>Inspecter les fichiers de modèle générés

Le travail stocke les fichiers de modèle générés dans le répertoire de sortie avec l’attribut `id` égal à `MODEL`, vous pouvez répertorier les fichiers de modèle et obtenir les URL de téléchargement à l’aide de la commande suivante :

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

Exemple de sortie :
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Ou bien, vous pouvez utiliser le portail ou l’Explorateur de stockage Azure pour inspecter les fichiers générés. Pour distinguer la sortie de différents travaux, Batch AI crée une structure de dossiers unique pour chacun d’eux. Vous pouvez trouver le chemin d’accès vers le dossier contenant la sortie à l’aide de l’attribut `jobOutputDirectoryPathSegment` du travail envoyé :

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Exemple de sortie :
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>Supprimer des ressources

Supprimez le groupe de ressources et toutes les ressources attribuées avec la commande suivante :

```azurecli
az group delete -n batchai.quickstart -y
```
