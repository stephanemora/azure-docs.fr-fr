---
title: Démarrage rapide Azure – Apprentissage Deep Learning – Azure CLI | Microsoft Docs
description: Démarrage rapide – Apprenez rapidement à entraîner un réseau de neurones de Deep Learning TensorFlow sur un seul GPU avec Batch AI à l’aide d’Azure CLI.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408066"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Démarrage rapide : Entraîner un modèle de Deep Learning avec Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ce démarrage rapide montre comment entraîner un exemple de modèle de Deep Learning sur une machine virtuelle compatible GPU gérée par Batch AI. Batch AI est un service géré qui permet aux scientifiques des données et aux chercheurs en IA d’effectuer l’apprentissage de modèles d’IA et de Machine Learning à grande échelle sur des clusters de machines virtuelles Azure. 

Cet exemple utilise Azure CLI pour configurer Batch AI de façon à entraîner un exemple de réseau de neurones [TensorFlow](https://www.tensorflow.org/) sur la [base de données MNIST](http://yann.lecun.com/exdb/mnist/) de chiffres manuscrits. À l’issue de ce démarrage rapide, vous comprendrez les concepts fondamentaux liés à l’utilisation de Batch AI pour effectuer l'apprentissage d’un modèle d’IA ou de Machine Learning. Vous pourrez alors essayer d’entraîner différents modèles à plus grande échelle.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, ce démarrage rapide exige au minimum la version 2.0.38 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

Ce démarrage rapide suppose d’exécuter les commandes dans un interpréteur de commandes Bash, soit dans Cloud Shell soit sur un ordinateur local. Si vous avez déjà suivi le guide de démarrage rapide [Créer un cluster Batch AI avec Azure CLI](quickstart-create-cluster-cli.md), ignorez les deux premières étapes (créer un groupe de ressources et un cluster Batch AI).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande `az group create`. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus2*. Choisissez bien l’emplacement USA Est 2, ou un autre emplacement dans lequel le service Batch AI est disponible. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Créer un cluster Batch AI

Tout d’abord, utilisez la commande `az batchai workspace create` pour créer un *espace de travail* Batch AI. Il vous faut un espace de travail pour organiser vos clusters Batch AI et autres ressources.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Pour créer un cluster Batch AI, utilisez la commande `az batchai cluster create`. L’exemple suivant crée un cluster à un nœud avec les propriétés suivantes :

* Utilise la taille de machine virtuelle NC6, qui comporte un GPU NVIDIA Tesla K80. Azure propose plusieurs tailles de machines virtuelles avec différents GPU NVIDIA.
* Exécute une image de serveur Ubuntu par défaut conçue pour héberger des applications en conteneur. Vous pouvez exécuter la plupart des charges de travail d’apprentissage sur cette distribution. 
* Ajoute un compte d’utilisateur nommé *myusername* et génère des clés SSH si elles n’existent pas déjà dans l’emplacement de clé par défaut (*~/.ssh*) de votre environnement local.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

La sortie de la commande affiche les propriétés du cluster. La création et le démarrage du nœud prennent quelques minutes. Pour afficher l’état du cluster, exécutez la commande `az batchai cluster show`.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

Au début de la création du cluster, la sortie se présente ainsi, avec le cluster `resizing` :

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Passez aux étapes suivantes pour télécharger le script d’apprentissage et créer la tâche d’apprentissage du cluster pendant l’évolution de son état. Le cluster est prêt à exécuter la tâche d’apprentissage lorsque l’état indique `steady` et que l’unique nœud est `Idle`.

## <a name="upload-training-script"></a>Charger le script d’apprentissage

Utilisez la commande `az storage account create` pour créer un compte de stockage servant à stocker votre script d’apprentissage et la sortie correspondante.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Créez un partage de fichiers Azure nommé `myshare` dans le compte avec la commande `az storage share create` :

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Utilisez la commande `az storage directory create` pour créer des répertoires dans le partage de fichiers Azure. Créez le répertoire `scripts` pour le script d’apprentissage, et `logs` pour la sortie de l’apprentissage :

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

Dans votre interpréteur de commandes Bash, créez un répertoire de travail local et téléchargez l’exemple [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) TensorFlow. Ce script Python effectue l’apprentissage d’un réseau de neurones convolutif sur le jeu d’images MNIST de 60 000 chiffres manuscrits de 0 à 9. Il teste ensuite le modèle sur un ensemble d’exemples de test.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Chargez le script dans le répertoire `scripts` du partage à l’aide de la commande `az storage file upload`.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Soumettre un travail de formation

Commencez par créer une *expérience* Batch AI dans votre espace de travail avec la commande `az batchai experiment create`. Un essai est un conteneur logique pour les travaux Batch AI connexes.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Dans votre répertoire de travail, créez un fichier de configuration de la tâche d’apprentissage `job.json` comportant le contenu suivant. Ce fichier de configuration sera passé en argument lors de la soumission de la tâche d’apprentissage.

Ce fichier `job.json` comporte des paramètres permettant de localiser le fichier de script Python et de l’exécuter dans un conteneur TensorFlow sur le nœud GPU. Il précise également où enregistrer les fichiers de sortie de la tâche dans le stockage Azure. `<AZURE_BATCHAI_STORAGE_ACCOUNT>` indique que le nom du compte de stockage sera spécifié lors de la soumission de la tâche.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Utilisez la commande `az batchai job create` pour soumettre la tâche sur le nœud, en passant en argument le fichier de configuration `job.json` et le nom de votre compte de stockage :

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

La commande retourne les propriétés de la tâche, puis se termine au bout de quelques minutes. Pour surveiller la progression de cette tâche, utilisez la commande `az batchai job file stream` afin de diffuser en continu le fichier `stdout-wk-0.txt` à partir du répertoire de sortie standard sur le nœud. Le script d’apprentissage génère ce fichier au moment où la tâche commence à s’exécuter.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Exemple de sortie :

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

La diffusion en continu s’arrête à la fin de la tâche. L’exemple de script effectue l’apprentissage sur *10 époques* ou traverse le jeu de données d’apprentissage. Dans cet exemple, après 10 époques, le modèle entraîné obtient un résultat de seulement 0,8 % au test d’erreur.

## <a name="get-job-output"></a>Récupérer la sortie de la tâche

Batch AI crée une structure de dossiers unique dans le compte de stockage pour la sortie de chaque tâche. Définissez la variable d’environnement JOB_OUTPUT_PATH selon ce chemin d’accès. Ensuite, listez les fichiers de sortie dans le stockage avec la commande `az storage file list` :

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

Le résultat se présente ainsi :

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Utilisez la commande `az storage file download` pour télécharger un ou plusieurs fichiers dans votre répertoire de travail local. Par exemple : 

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez suivre d’autres tutoriels et exemples Batch AI, utilisez l’espace de travail, le cluster et le compte de stockage Batch AI créés dans ce démarrage rapide. 

Le cluster Batch AI occasionne des frais lorsque les nœuds sont en cours d’exécution. Si vous souhaitez conserver la configuration du cluster alors que vous n’avez aucune tâche à exécuter, redimensionnez le cluster sur zéro nœud. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Par la suite, redimensionnez-le sur un ou plusieurs nœuds pour exécuter vos tâches. Lorsque vous n’avez plus besoin du cluster, supprimez-le avec la commande `az batchai cluster delete` :

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande `az group delete` pour supprimer le groupe de ressources Batch AI et de stockage. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris à utiliser Batch AI pour effectuer l’apprentissage d’un exemple de modèle de Deep Learning TensorFlow sur une machine virtuelle à un seul GPU, avec Azure CLI. Pour savoir plus en détail comment distribuer l’apprentissage du modèle sur un cluster de GPU plus grand, passez au tutoriel Batch AI.

> [!div class="nextstepaction"]
> [Tutoriel de l’apprentissage distribué](./tutorial-horovod-tensorflow.md)