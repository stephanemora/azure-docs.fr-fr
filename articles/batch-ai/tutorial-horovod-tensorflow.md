---
title: Tutoriel – Apprentissage distribué avec Azure Batch AI et Horovod | Microsoft Docs
description: 'Tutoriel : Guide pratique pour entraîner un modèle distribué avec Horovod à l’aide du service Azure Batch AI et d’Azure CLI.'
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 45255845d8645391ee33471830ac2ef27870a40d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408627"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Didacticiel : Effectuer l’apprentissage d’un modèle distribué avec Horovod

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ce tutoriel a pour objectif d’entraîner un modèle de Deep Learning distribué en l’exécutant en parallèle sur plusieurs nœuds dans un cluster Batch AI. Batch AI est un service géré permettant d’effectuer l’apprentissage de modèles de Machine Learning et d’IA à grande échelle sur des clusters de GPU Azure. 

Ce tutoriel présente un flux de travail Batch AI courant, et explique comment interagir avec les ressources Batch AI à l’aide d’Azure CLI. Sont abordés les sujets suivants :

> [!div class="checklist"]
> * Configurer un espace de travail, une expérience et un cluster Batch AI
> * Configurer un partage de fichiers Azure pour l’entrée et la sortie
> * Paralléliser un modèle de Deep Learning avec Horovod
> * Soumettre une tâche d’apprentissage
> * Surveiller la tâche
> * Récupérer les résultats de l’apprentissage

Dans le cadre de ce tutoriel, un modèle de détection d’objets est modifié de façon à s’exécuter en parallèle avec [Horovod](https://github.com/uber/horovod). L’apprentissage du modèle est effectué sur le [jeu de données CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) d’images. La tâche d’apprentissage s’exécute sur un cluster contenant 24 processeurs virtuels et 4 GPU et prend environ 60 minutes.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, ce tutoriel exige au minimum la version 2.0.38 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Pourquoi utiliser Horovod ?

Horovod est un framework d’apprentissage distribué pour Tensorflow, Keras et PyTorch, utilisé dans ce tutoriel. Il permet de convertir un script d’apprentissage conçu pour s’exécuter sur un seul GPU en un script qui fonctionne efficacement sur un système distribué, grâce à quelques lignes de code seulement.

Outre Horovod, Batch AI prend en charge l’apprentissage distribué avec différents frameworks open source populaires. Consultez les termes du contrat de licence du framework que vous utilisez pour entraîner des modèles en production.

## <a name="prepare-the-batch-ai-environment"></a>Préparer l’environnement Batch AI

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Utilisez la commande `az group create` pour créer un groupe de ressources nommé `batchai.horovod` dans la région `eastus`. Ce groupe de ressources permet de déployer des ressources Batch AI.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Créer un espace de travail

Créez un espace de travail Batch AI avec la commande `az batchai workspace create`. Un espace de travail est une collection de premier niveau d’autres ressources Batch AI. La commande suivante crée un espace de travail nommé `batchaidev` sous votre groupe de ressources.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Création d'une expérience

Une expérience Batch AI regroupe une ou plusieurs tâches interrogées et gérées ensemble. La commande `az batchai experiment create` suivante crée une expérience nommée `cifar` sous l’espace de travail et le groupe de ressources.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>Configurer un cluster de GPU

Ensuite, configurez un cluster de GPU pour exécuter l’expérience. Batch AI offre une gamme flexible d’options de personnalisation des clusters en fonction des besoins.

La commande `az batchai cluster create` suivante crée un cluster à quatre nœuds nommé `nc6cluster` sous votre espace de travail et votre groupe de ressources. Par défaut, les machines virtuelles du cluster exécutent une image de serveur Ubuntu conçue pour héberger des applications en conteneur. Les nœuds de cluster de cet exemple utilisent la taille `Standard_NC6`, qui contient un GPU NVIDIA Tesla K80.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

Exécutez la commande `az batchai cluster show` pour afficher l’état du cluster. Le provisionnement du cluster prend généralement quelques minutes.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

Au début de sa création, le cluster se trouve dans l’état `resizing`. Passez aux étapes suivantes pendant l’évolution de cet état. Le cluster est prêt à exécuter la tâche d’apprentissage lorsque l’état est `steady` et les nœuds `idle`. Par exemple : 

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Configurer le stockage

Utilisez la commande `az storage account create` pour créer un compte de stockage servant à stocker votre script d’apprentissage et la sortie correspondante.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Créez un partage de fichiers Azure nommé `myshare` dans le compte avec la commande `az storage share create` :

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

Dans la pratique, ce même stockage peut être utilisé pour plusieurs tâches et plusieurs expériences. Dans un souci d’organisation, créez un répertoire dans le partage de fichiers pour stocker les fichiers associés à cette expérience en particulier. La commande `az storage directory create` suivante crée un répertoire nommé `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

L’étape suivante consiste à préparer le script d’apprentissage proprement dit, puis à le charger dans le répertoire que vous venez de créer.

## <a name="create-the-training-script"></a>Créer le script d’apprentissage

Cette expérience consiste à exécuter un script Python qui a subi quelques modifications de façon à faire tourner un modèle de détection d’objets en parallèle de Horovod. Le [modèle d’origine](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) utilise Keras avec un back end TensorFlow. 

Dans un répertoire de travail, dans votre interpréteur de commandes, utilisez l’éditeur de texte de votre choix pour créer un fichier nommé `cifar_cnn_distributed.py` et comportant le contenu suivant. Les modifications apportées au code source d’origine sont mises en commentaire avec un préfixe `HOROVOD`.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

Comme on le voit dans cet exemple, une légère mise à jour du modèle suffit pour mettre en place l’apprentissage distribué à l’aide du framework Horovod. 

N’oubliez pas que ce script utilise un modèle et un jeu de données relativement petits à des fins de démonstration. Par conséquent, ce modèle distribué n’affichera pas nécessairement une amélioration sensible des performances. Pour voir réellement toute la puissance de l’apprentissage distribué, utilisez un modèle et un jeu de données beaucoup plus volumineux.

## <a name="upload-the-training-script"></a>Charger le script d’apprentissage

Lorsque le script est prêt, il reste à le charger dans le répertoire de partage de fichiers que vous avez créé tout à l’heure. La commande `az storage file upload` le transfère au bon emplacement à partir du répertoire de travail local.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>Soumettre la tâche d’apprentissage

Après avoir suivi les étapes précédentes, créez une tâche d’apprentissage. Dans Batch AI, on utilise un fichier `job.json` pour définir les paramètres d’exécution d’une tâche. Dans l’éditeur de texte de votre choix, créez un fichier de configuration de tâche nommé `job.json` et comportant le contenu suivant.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Explication des propriétés :

| Propriété | Description |
| --------- | --------- |
| `nodeCount` | Nombre de nœuds à dédier à la tâche. Cette tâche s’exécutera en parallèle sur `4` nœuds. |
| `horovodSettings` | Le champ `pythonScriptFilePath` définit le chemin d’accès au script Horovod, situé dans le répertoire `cifar` créé précédemment. Le champ `commandLineArgs` correspond aux arguments de ligne de commande servant à exécuter le script. Dans cette expérience, le répertoire dans lequel le modèle sera enregistré est le seul argument obligatoire. `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` est le chemin d’accès où le partage de fichiers a été monté. | 
| `stdOutErrPathPrefix` | Chemin d’accès pour stocker les journaux et les sorties de la tâche ; dans cet exemple, le même répertoire `cifar`. |
| `jobPreparation` | Instructions spéciales pour la préparation de l’environnement d’exécution de la tâche. Ce script nécessite l’installation des packages MPI et Horovod indiqués. |
| `containerSettings` | Paramètres du conteneur sur lequel s’exécute la tâche. Cette tâche utilise un conteneur Docker avec `tensorflow`.

Créez la tâche avec la commande `az batchai job create` suivant la configuration. La commande suivante met en file d’attente une tâche nommée `cifar_distributed` à l’aide de toutes les ressources qui ont été définies à ce stade. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Si les nœuds sont actuellement occupés, la tâche peut mettre un certain temps à s’exécuter. Utilisez la commande `az batchai job show` pour voir son état d’exécution.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Visualiser l’apprentissage distribué

Dès que la tâche commence à s’exécuter, utilisez de nouveau la commande `az batchai cluster show` pour demander l’état des nœuds de cluster. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

La sortie devrait indiquer qu’ils sont tous les quatre en cours d’exécution, comme ci-dessous. Ce résultat montre qu’ils sont actuellement utilisés dans l’apprentissage distribué.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>Surveiller la tâche

### <a name="list-output-files"></a>Lister les fichiers de sortie

Pendant l’exécution de la tâche, utilisez la commande `az batchai job file list` pour lister les fichiers de sortie qu’elle génère.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

Dans le cadre de cette expérience, la sortie devrait se présenter comme ci-dessous. L’ensemble de la sortie de la tâche est consigné dans `stdout.txt`, tandis que `stderr.txt` affiche les erreurs qui se produisent dans l’exécution principale. Les autres fichiers sont les journaux de sortie, d’erreurs et de préparation de la tâche correspondant à chacun des nœuds.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Transmettre en continu un fichier de sortie

Utilisez la commande `az batchai job file stream` pour transmettre en continu le contenu d’un fichier. L’exemple suivant effectue cette opération pour le journal de sortie principal.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

Pendant l’exécution de la tâche, la commande diffuse en continu la sortie standard de la tâche d’apprentissage, affichant une sortie de ce type.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

Le script effectue l’apprentissage sur 25 époques ou passe par le jeu de données d’apprentissage. Ce processus prend environ 60 minutes. 

## <a name="retrieve-the-results"></a>Récupérer les résultats

Une fois le script terminé, si tout s’est bien passé, la précision de validation doit être de l’ordre de 70-75 % ; le modèle entraîné est enregistré dans le partage de fichiers à l’emplacement `cifar/saved_models/keras_cifar10_trained_model.h5`. 

L’apprentissage du modèle fait généralement partie d’un workflow plus large. Vous pourriez par exemple exposer le modèle entraîné dans une autre application. Pour télécharger localement le modèle entraîné, utilisez la commande `az storage file download`. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Supprimer les ressources

Une fois les tâches terminées, la meilleure pratique pour limiter les coûts de calcul consiste à réduire l’échelle de tous les clusters à `0 nodes` afin de ne pas se voir facturer la durée d’inactivité. Utilisez la commande `az batchai cluster resize` suivante. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Par la suite, redimensionnez-le sur un ou plusieurs nœuds pour exécuter vos tâches. 

Si vous ne pensez pas réutiliser l’espace de travail et le compte de stockage à l’avenir, supprimez le groupe de ressources avec la commande `az group delete`. La suppression d’un groupe de ressources entraîne la suppression de toutes les ressources qui en font partie.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un espace de travail, une expérience et un cluster Batch AI
> * Configurer un partage de fichiers Azure pour l’entrée et la sortie
> * Paralléliser un modèle avec Horovod
> * Soumettre une tâche d’apprentissage
> * Surveiller la tâche
> * Récupérer les résultats de l’apprentissage

Pour voir des exemples d’utilisation de Batch AI avec différents frameworks, consultez les recettes sur GitHub.

> [!div class="nextstepaction"]
> [Recettes Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)
