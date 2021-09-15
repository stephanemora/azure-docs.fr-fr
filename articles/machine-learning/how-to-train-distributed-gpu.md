---
title: Guide de l’entraînement de GPU distribué
titleSuffix: Azure Machine Learning
description: Découvrez les meilleures pratiques en matière d’entraînement distribué avec les frameworks pris en charge par Azure Machine Learning, tels que MPI, Horovod, DeepSpeed, PyTorch, PyTorch Lightning, Hugging Face Transformers, TensorFlow et InfiniBand.
author: fuhuifang
ms.author: fufang
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: ff95cb0004fbde34e70bcb778d93a2f381dc5830
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122661653"
---
# <a name="distributed-gpu-training-guide"></a>Guide de l’entraînement de GPU distribué

Découvrez-en plus sur l’utilisation du code d’entraînement de GPU distribué dans Azure Machine Learning (ML). Cet article ne vous apprend pas l’entraînement distribué.  Il vous aide à exécuter votre code d’entraînement distribué existant sur Azure Machine Learning. Il fournit des conseils et des exemples à suivre pour chaque framework :

* Interface de passage de messages (MPI)
    * Horovod
    * DeepSpeed
    * Variables d’environnement à partir d’Open MPI
* PyTorch
    * Initialisation du groupe de processus
    * Options de lancement
    * DistributedDataParallel (lancement par processus)
    * Utilisation de `torch.distributed.launch` (lancement par nœud)
    * PyTorch Lightning
    * Hugging Face Transformers
* TensorFlow
    * Variables d’environnement pour TensorFlow (TF_CONFIG)
* Entraînement de GPU accéléré avec InfiniBand

## <a name="prerequisites"></a>Prérequis

Passez en revue les [concepts de base de l’entraînement de GPU distribué](concept-distributed-training.md), comme le _parallélisme des données_, le _parallélisme des données distribué_ et le _parallélisme des modèles_.

> [!TIP]
> Si vous ne connaissez pas le type de parallélisme à utiliser, plus de 90 % du temps, vous devez utiliser le __parallélisme des données distribué__.

## <a name="mpi"></a>MPI

Azure ML fournit un [travail MPI](https://www.mcs.anl.gov/research/projects/mpi/) qui permet de lancer un nombre donné de processus dans chaque nœud. Vous pouvez adopter cette approche pour exécuter un entraînement distribué en utilisant un lanceur par processus ou un lanceur par nœud, selon que `process_count_per_node` a la valeur 1 (valeur par défaut) pour le lanceur par nœud ou est égal au nombre d’appareils/GPU pour le lanceur par processus. Azure ML construit la commande de lancement MPI complète (`mpirun`) en arrière-plan.  Vous ne pouvez pas fournir vos propres commandes de lanceur de nœud principal complètes, telles que `mpirun` ou `DeepSpeed launcher`.

> [!TIP]
> Une bibliothèque MPI doit être installée sur l’image Docker de base utilisée par un travail MPI Azure Machine Learning. [Open MPI](https://www.open-mpi.org/) est inclus dans toutes les [images de base de GPU AzureML](https://github.com/Azure/AzureML-Containers). Quand vous utilisez une image Docker personnalisée, vous devez vous assurer que l’image comprend une bibliothèque MPI. Open MPI est recommandé, mais vous pouvez également utiliser une autre implémentation MPI telle qu’Intel MPI. Azure ML fournit également des [environnements organisés](resource-curated-environments.md) pour les frameworks populaires. 

Pour exécuter un entraînement distribué avec MPI, procédez comme suit :

1. Utilisez un environnement Azure ML avec le framework de Deep Learning par défaut et MPI. Azure ML fournit un [environnement organisé](resource-curated-environments.md) pour les frameworks populaires.
1. Définissez `MpiConfiguration` avec `process_count_per_node` et `node_count`. `process_count_per_node` doit être égal au nombre de GPU par nœud pour le lancement par processus, ou défini sur 1 (valeur par défaut) pour le lancement par nœud si le script utilisateur est chargé de lancer les processus par nœud.
1. Passez l’objet `MpiConfiguration` au paramètre `distributed_job_config` de `ScriptRunConfig`.

```python
from azureml.core import Workspace, ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import MpiConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = MpiConfiguration(process_count_per_node=4, node_count=2)

run_config = ScriptRunConfig(
  source_directory= './src',
  script='train.py',
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

### <a name="horovod"></a>Horovod

Utilisez la configuration du travail MPI quand vous utilisez [Horovod](https://horovod.readthedocs.io/en/stable/index.html) pour un entraînement distribué avec le framework de Deep Learning.

Assurez-vous que votre code suit ces conseils :

* Le code d’entraînement est correctement instrumenté avec Horovod avant l’ajout des composants ML Azure.
* Votre environnement Azure ML contient Horovod et MPI. Les environnements de GPU organisés PyTorch et TensorFlow sont préconfigurés avec Horovod et ses dépendances.
* Créez une configuration `MpiConfiguration` avec la distribution souhaitée.

### <a name="horovod-example"></a>Exemple Horovod

* [azureml-examples : entraînement distribué TensorFlow avec Horovod](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed-horovod)

### <a name="deepspeed"></a>DeepSpeed

N’utilisez pas le lanceur personnalisé de DeepSpeed pour exécuter un entraînement distribué avec la bibliothèque [DeepSpeed](https://www.deepspeed.ai/) sur Azure ML. Au lieu de cela, configurez un travail MPI pour lancer le travail d’entraînement [avec MPI](https://www.deepspeed.ai/getting-started/#mpi-and-azureml-compatibility).

Assurez-vous que votre code suit ces conseils :

* Votre environnement Azure ML contient DeepSpeed et ses dépendances, Open MPI et mpi4py.
* Créez une configuration `MpiConfiguration` avec votre distribution.

### <a name="deepseed-example"></a>Exemple DeepSeed

* [azureml-examples : entraînement distribué avec DeepSpeed sur CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/deepspeed/cifar)

### <a name="environment-variables-from-open-mpi"></a>Variables d’environnement à partir d’Open MPI

Lors de l’exécution de travaux MPI avec des images Open MPI, les variables d’environnement suivantes sont lancées pour chaque processus :

1. `OMPI_COMM_WORLD_RANK` : rang du processus
2. `OMPI_COMM_WORLD_SIZE` : nombre total de processus (WORLD_SIZE)
3. `AZ_BATCH_MASTER_NODE` : adresse principale avec port, `MASTER_ADDR:MASTER_PORT`
4. `OMPI_COMM_WORLD_LOCAL_RANK` : rang local du processus sur le nœud
5. `OMPI_COMM_WORLD_LOCAL_SIZE` : nombre de processus sur le nœud

> [!TIP]
> Malgré son nom, la variable d’environnement `OMPI_COMM_WORLD_NODE_RANK` ne correspond pas à `NODE_RANK`. Pour utiliser le lanceur par nœud, définissez `process_count_per_node=1` et utilisez `OMPI_COMM_WORLD_RANK` comme `NODE_RANK`.

## <a name="pytorch"></a>PyTorch

Azure ML prend en charge l’exécution de travaux distribués avec des fonctionnalités d’entraînement distribué natives de PyTorch (`torch.distributed`).

> [!TIP]
> Pour le parallélisme des données, l’[aide officielle de PyTorch](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#comparison-between-dataparallel-and-distributeddataparallel) stipule d’utiliser DistributedDataParallel (DDP) plutôt que DataParallel pour l’entraînement distribué mononœud et multinœud. De plus, PyTorch [recommande d’utiliser DistributedDataParallel plutôt que le package de multitraitement](https://pytorch.org/docs/stable/notes/cuda.html#use-nn-parallel-distributeddataparallel-instead-of-multiprocessing-or-nn-dataparallel). La documentation et les exemples Azure Machine Learning se concentrent donc sur l’entraînement DistributedDataParallel.

### <a name="process-group-initialization"></a>Initialisation du groupe de processus

L’épine dorsale d’un entraînement distribué repose sur un groupe de processus qui se connaissent entre eux et peuvent communiquer entre eux avec un back-end. Pour PyTorch, le groupe de processus est créé en appelant [torch.distributed.init_process_group](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) dans __tous les processus distribués__ pour former collectivement un groupe de processus.

```
torch.distributed.init_process_group(backend='nccl', init_method='env://', ...)
```

Les back-ends de communication les plus couramment utilisés sont `mpi`, `nccl` et `gloo`. Pour l’entraînement basé sur le GPU, `nccl` est recommandé pour des performances optimales et doit être utilisé chaque fois que cela est possible. 

`init_method` indique comment chaque processus peut découvrir les autres processus et comment ils initialisent et vérifient le groupe de processus en utilisant le back-end de communication. Par défaut, si `init_method` n’est pas spécifié, PyTorch utilise la méthode d’initialisation par variable d’environnement (`env://`). `init_method` est la méthode d’initialisation recommandée à utiliser dans votre code d’entraînement pour exécuter un travail PyTorch distribué sur Azure ML.  PyTorch recherche les variables d’environnement suivantes pour l’initialisation :

- **`MASTER_ADDR`** – Adresse IP de la machine destinée à héberger le processus avec le rang 0.
- **`MASTER_PORT`** – Port libre sur la machine destinée à héberger le processus avec le rang 0.
- **`WORLD_SIZE`** – Nombre total de processus. Doit être égal au nombre total d’appareils (GPU) utilisés pour l’entraînement distribué.
- **`RANK`** – Rang (mondial) du processus en cours. Les valeurs possibles sont comprises entre 0 et (l’échelle mondiale - 1).

Pour plus d’informations sur l’initialisation du groupe de processus, consultez la [documentation PyTorch](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group).

Au-delà de ces variables, de nombreuses applications ont également besoin des variables d’environnement suivantes :
- **`LOCAL_RANK`** – Rang local (relatif) du processus dans le nœud. Les valeurs possibles sont comprises entre 0 et (le nombre de processus sur le nœud - 1). Ces informations sont utiles, car de nombreuses opérations, telles que la préparation des données, doivent être effectuées une fois par nœud, généralement avec local_rank = 0.
- **`NODE_RANK`** – Rang du nœud dans l’entraînement sur plusieurs nœuds. Les valeurs possibles sont comprises entre 0 et (le nombre total de nœuds - 1).

### <a name="pytorch-launch-options"></a>Options de lancement de PyTorch

Le travail PyTorch Azure ML prend en charge deux types d’options pour lancer l’entraînement distribué :

- __Lanceur par processus__ : le système lance tous les processus distribués pour vous, avec toutes les informations pertinentes (telles que les variables d’environnement) pour configurer le groupe de processus.
- __Lanceur par nœud__ : vous fournissez à ML Azure le lanceur d’utilitaire qui doit s’exécuter sur chaque nœud. Le lanceur d’utilitaire gère le lancement de chacun des processus sur un nœud donné. Au sein même de chaque nœud,`RANK` et `LOCAL_RANK` sont configurés par le lanceur. L’utilitaire **torch.distributed.launch** et PyTorch Lightning appartiennent tous deux à cette catégorie.

Il n’existe aucune différence fondamentale entre ces options de lancement. Tout dépend plutôt de votre préférence ou des conventions des frameworks et des bibliothèques s’appuyant sur vanilla PyTorch (par exemple, Lightning ou Hugging Face).

Les sections suivantes décrivent plus en détail la configuration des travaux Azure ML PyTorch pour chacune des options de lancement.

### <a name="distributeddataparallel-per-process-launch"></a><a name="per-process-launch"></a> DistributedDataParallel (lancement par processus)

Vous n’avez pas besoin de recourir à un utilitaire de lancement comme `torch.distributed.launch`. Pour exécuter un travail PyTorch distribué :

1. Spécifiez le script et les arguments d’entraînement.
1. Créez une configuration `PyTorchConfiguration` et spécifiez `process_count` et `node_count`. `process_count` correspond au nombre total de processus que vous souhaitez exécuter pour votre travail. `process_count` doit généralement être égal à `# GPUs per node x # nodes`. Si `process_count` n’est pas spécifié, Azure ML lance par défaut un processus par nœud.

Azure ML définit les variables d’environnement `MASTER_ADDR`, `MASTER_PORT`, `WORLD_SIZE` et `NODE_RANK` sur chaque nœud et définit les variables d’environnement `RANK` et `LOCAL_RANK` de niveau processus.

Pour utiliser cette option dans le cas d’un entraînement avec plusieurs processus par nœud, utilisez le SDK Azure ML Python `>= 1.22.0`. Process_count a été introduit dans la version 1.22.0.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=8, node_count=2)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 50],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> Si votre script d’entraînement passe des informations, telles que le rang local ou le rang en tant qu’arguments de script, vous pouvez référencer la ou les variables d’environnement dans les arguments :
>
> ```python
> arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]
> ```

### <a name="pytorch-per-process-launch-example"></a>Exemple de lancement par processus Pytorch

- [azureml-examples : entraînement distribué avec PyTorch sur CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="using-torchdistributedlaunch-per-node-launch"></a><a name="per-node-launch"></a> Utilisation de `torch.distributed.launch` (lancement par nœud)

PyTorch fournit un utilitaire de lancement dans [torch.distributed.launch](https://pytorch.org/docs/stable/distributed.html#launch-utility), dont vous pouvez vous servir pour lancer plusieurs processus par nœud. Le module `torch.distributed.launch` génère plusieurs processus d’entraînement sur chacun des nœuds.

Les étapes suivantes montrent comment configurer un travail PyTorch avec un lanceur par nœud sur Azure ML.  Le travail revient à exécuter la commande suivante :

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Fournissez la commande `torch.distributed.launch` au paramètre `command` du constructeur `ScriptRunConfig`. Azure ML exécute cette commande sur chaque nœud de votre cluster d’entraînement. `--nproc_per_node` doit être inférieur ou égal au nombre de GPU disponibles sur chaque nœud. MASTER_ADDR, MASTER_PORT et NODE_RANK étant tous définis par Azure ML, vous pouvez vous contenter de référencer les variables d’environnement dans la commande. Azure ML définit MASTER_PORT sur `6105`, mais vous pouvez passer une valeur différente à l’argument `--master_port` de la commande torch.distributed.launch si vous le souhaitez. (L’utilitaire de lancement réinitialisera les variables d’environnement.)
2. Créez une configuration `PyTorchConfiguration` et spécifiez `node_count`.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

> [!TIP]
> **Entraînement multi-GPU mononœud :** si vous recourez à l’utilitaire de lancement pour exécuter un entraînement PyTorch multi-GPU mononœud, vous n’avez pas besoin de spécifier le paramètre `distributed_job_config` de ScriptRunConfig.
>
>```python
> launch_cmd = "python -m torch.distributed.launch --nproc_per_node 4 --use_env train.py --epochs 50".split()
>
> run_config = ScriptRunConfig(
>  source_directory='./src',
>  command=launch_cmd,
>  compute_target=compute_target,
>  environment=pytorch_env,
> )
> ```

### <a name="pytorch-per-node-launch-example"></a>Exemple PyTorch de lancement par nœud

- [azureml-examples : entraînement distribué avec PyTorch sur CIFAR-10](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/pytorch/cifar-distributed)

### <a name="pytorch-lightning"></a>PyTorch Lightning

[PyTorch Lightning](https://pytorch-lightning.readthedocs.io/en/stable/) est une bibliothèque open source légère qui fournit une interface de haut niveau pour PyTorch. Lightning fait abstraction de nombreuses configurations d’entraînement distribué de niveau inférieur nécessaires pour vanilla PyTorch. Lightning vous permet d’exécuter vos scripts d’entraînement dans des configurations mono-GPU, multi-GPU mononœud et multi-GPU multinœuds. En arrière-plan, il lance plusieurs processus pour vous similaires à `torch.distributed.launch`.

Dans le cas d’un entraînement mononœud (y compris multi-GPU mononœud), vous pouvez exécuter votre code sur Azure ML sans avoir à spécifier un paramètre `distributed_job_config`. Dans le cas d’un entraînement multinœud, Lightning nécessite que les variables d’environnement suivantes soient définies sur chaque nœud de votre cluster d’entraînement :

- MASTER_ADDR
- MASTER_PORT
- NODE_RANK

Pour exécuter un entraînement Lightning multinœud sur Azure ML, vous pouvez en grande partie suivre le [guide du lancement par nœud](#per-node-launch) :

- Définissez la `PyTorchConfiguration` et spécifiez le paramètre `node_count`. Ne spécifiez pas `process_count`, car Lightning gère en interne le lancement des processus Worker pour chaque nœud.
- Pour les travaux PyTorch, Azure ML gère la définition des variables d’environnement MASTER_ADDR, MASTER_PORT et NODE_RANK requises par Lightning.
- Lightning gère le calcul du nombre total de processus (WORLD_SIZE) à partir des indicateurs `--gpus` et `--num_nodes` du module d’entraînement et gère le rang et le rang local en interne.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.runconfig import PyTorchConfiguration

nnodes = 2
args = ['--max_epochs', 50, '--gpus', 2, '--accelerator', 'ddp', '--num_nodes', nnodes]
distr_config = PyTorchConfiguration(node_count=nnodes)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=args,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(run_config)
```

### <a name="pytorch-lightning-example"></a>Exemple PyTorch Lightning

* [azureml-examples : entraînement multinœud avec PyTorch Lightning](https://github.com/Azure/azureml-examples/blob/main/python-sdk/experimental/using-pytorch-lightning/4.train-multi-node-ddp.ipynb)

### <a name="hugging-face-transformers"></a>Hugging Face Transformers

Hugging Face fournit de nombreux [exemples](https://github.com/huggingface/transformers/tree/master/examples) d’utilisation de sa bibliothèque Transformers avec `torch.distributed.launch` pour exécuter un entraînement distribué. Pour exécuter ces exemples et vos propres scripts d’entraînement personnalisés avec l’API du module d’entraînement de Transformers, suivez la section [Utilisation de `torch.distributed.launch`](#per-node-launch).

Exemple de code de configuration de travail pour affiner le grand modèle BERT sur la tâche MNLI de classification de texte avec le script `run_glue.py` sur un nœud doté de 8 GPU :

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

distr_config = PyTorchConfiguration() # node_count defaults to 1
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 8 text-classification/run_glue.py --model_name_or_path bert-large-uncased-whole-word-masking --task_name mnli --do_train --do_eval --max_seq_length 128 --per_device_train_batch_size 8 --learning_rate 2e-5 --num_train_epochs 3.0 --output_dir /tmp/mnli_output".split()

run_config = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)
```

Vous pouvez également utiliser l’option de [lancement par processus](#per-process-launch) pour exécuter un entraînement distribué sans utiliser `torch.distributed.launch`. Gardez à l’esprit que si vous utilisez cette méthode, les transformateurs [TrainingArguments](https://huggingface.co/transformers/main_classes/trainer.html?highlight=launch#trainingarguments) s’attendent à ce que le rang local soit passé comme argument (`--local_rank`). `torch.distributed.launch` s’occupe de cela quand `--use_env=False`, mais si vous utilisez le lancement par processus, vous devez passer explicitement le rang local comme argument au script d’entraînement `--local_rank=$LOCAL_RANK`, car Azure ML définit uniquement la variable d’environnement `LOCAL_RANK`.

## <a name="tensorflow"></a>TensorFlow

Si vous utilisez l’[entraînement TensorFlow distribué natif](https://www.tensorflow.org/guide/distributed_training) dans votre code d’entraînement, par exemple l’API `tf.distribute.Strategy` TensorFlow 2.x, vous pouvez lancer le travail distribué par le biais d’Azure ML en utilisant le `TensorflowConfiguration`.

Pour ce faire, spécifiez un objet `TensorflowConfiguration` comme paramètre `distributed_job_config` du constructeur `ScriptRunConfig`. Si vous utilisez `tf.distribute.experimental.MultiWorkerMirroredStrategy`, spécifiez le `worker_count` dans le `TensorflowConfiguration` correspondant au nombre de nœuds de votre travail d’entraînement.

```python
from azureml.core import ScriptRunConfig, Environment, Experiment
from azureml.core.runconfig import TensorflowConfiguration

curated_env_name = 'AzureML-TensorFlow-2.3-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

run_config = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  compute_target=compute_target,
  environment=tf_env,
  distributed_job_config=distr_config,
)

# submit the run configuration to start the job
run = Experiment(ws, "experiment_name").submit(run_config)
```

Si votre script d’entraînement utilise la stratégie de serveur de paramètres pour l’entraînement distribué, par exemple, pour la version de TensorFlow 1.x héritée, vous devez également spécifier le nombre de serveurs de paramètres à utiliser dans le travail, par exemple `tf_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)`.

### <a name="tf_config"></a>TF_CONFIG

Dans TensorFlow, la variable d’environnement **TF_CONFIG** est requise pour l’entraînement sur plusieurs machines. Pour les travaux TensorFlow, Azure ML configure et définit la variable TF_CONFIG de manière appropriée pour chaque Worker avant d’exécuter votre script d’entraînement.

Si nécessaire, vous pouvez accéder à TF_CONFIG à partir de votre script d’entraînement : `os.environ['TF_CONFIG']`.

Exemple TF_CONFIG défini sur un nœud Worker principal :
```json
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

### <a name="tensorflow-example"></a>Exemple TensorFlow

- [azureml-examples : entraînement TensorFlow distribué avec MultiWorkerMirroredStrategy](https://github.com/Azure/azureml-examples/tree/main/python-sdk/workflows/train/tensorflow/mnist-distributed)

## <a name="accelerating-gpu-training-with-infiniband"></a><a name="infiniband"></a> Accélération de l’entraînement de GPU avec InfiniBand

Certaines séries de machines virtuelles Azure, en particulier les séries NC, ND et H, disposent désormais de machines virtuelles compatibles RDMA avec prise en charge de SR-IOV et d’InfiniBand. Ces machines virtuelles communiquent via le réseau InfiniBand à faible latence et à large bande passante, qui est beaucoup plus performant que la connectivité Ethernet. SR-IOV pour InfiniBand offre des performances quasi-complètes pour toute bibliothèque MPI (MPI est utilisé par de nombreux frameworks et outils d’entraînement distribué, y compris le logiciel NCCL de NVIDIA). Ces références SKU sont destinées à répondre aux besoins des charges de travail de Machine Learning gourmandes en ressources et avec accélération GPU. Pour plus d’informations, consultez [Accélération de l’entraînement distribué dans Azure Machine Learning avec SR-IOV](https://techcommunity.microsoft.com/t5/azure-ai/accelerating-distributed-training-in-azure-machine-learning/ba-p/1059050).

Si vous créez un cluster `AmlCompute` avec une de ces tailles compatibles InfiniBand et prenant en charge RDMA, par exemple `Standard_ND40rs_v2`, l’image du système d’exploitation est fournie avec le pilote Mellanox OFED nécessaire pour activer InfiniBand préinstallé et préconfiguré.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles Machine Learning sur Azure](how-to-deploy-and-where.md)
* [Déployer et scorer un modèle Machine Learning en utilisant un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md)
* [Architecture de référence de la formation du Deep Learning distribué dans Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
