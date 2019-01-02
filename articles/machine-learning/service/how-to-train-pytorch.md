---
title: Entraîner des modèles avec PyTorch
titleSuffix: Azure Machine Learning service
description: Découvrez comment effectuer un entraînement à nœud unique et un entraînement distribué sur des modèles PyTorch avec l’estimateur PyTorch
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a6401c6059d8f72f344021879828b01c9ce77169
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100545"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>Entraîner des modèles PyTorch avec le service Azure Machine Learning

Pour un entraînement de réseau neuronal profond à l’aide de PyTorch, Azure Machine Learning fournit une classe `PyTorch`personnalisée de l’`Estimator`. L’estimateur `PyTorch` du SDK Azure vous permet d’envoyer facilement des tâches d’entraînement PyTorch pour les exécutions à nœud unique et distribuées sur Azure Compute.

## <a name="single-node-training"></a>Entraînement à nœud unique
L’entraînement avec l’estimateur `PyTorch` est similaire à celui qui utilise [l’`Estimator` de base](how-to-train-ml-models.md). Ainsi, vous devez d’abord lire l’article sur les procédures pour bien comprendre les concepts qui y sont présentés.
  
Pour exécuter une tâche PyTorch, instanciez un objet `PyTorch`. Vous devez déjà avoir créé votre objet [cible de calcul](how-to-set-up-training-targets.md#amlcompute) `compute_target`, ainsi que votre objet [banque de données ](how-to-access-data.md) `ds`.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Ici, nous spécifions les paramètres suivants pour le constructeur PyTorch :
Paramètre | Description
--|--
`source_directory` |  Répertoire local qui contient l’ensemble du code nécessaire à la tâche d’entraînement. Ce dossier est copié de votre ordinateur local vers la cible de calcul distante
`script_params` |  Dictionnaire spécifiant les arguments de ligne de commande de votre script d’entraînement `entry_script`, sous la forme de paires <argument de ligne de commande, valeur>
`compute_target` |  Cible de calcul à distance sur laquelle votre script de formation s’exécute, ici un cluster de calcul Azure Machine Learning ([AmlCompute](how-to-set-up-training-targets.md#amlcompute))
`entry_script` |  Chemin de fichier (relatif à `source_directory`) du script d’entraînement à exécuter sur la cible de calcul distante. Ce fichier et tous les autres fichiers dont il dépend doivent se trouver dans ce dossier
`conda_packages` |  Liste des packages Python à installer via conda et dont a besoin votre script d’entraînement. Le constructeur a un autre paramètre appelé `pip_packages` que vous pouvez utiliser pour tous les packages pip nécessaires
`use_gpu` |  Définissez cet indicateur sur `True` afin d’utiliser le GPU pour l’entraînement. La valeur par défaut est `False`

Étant donné que vous utilisez l’estimateur `PyTorch`, le conteneur utilisé pour l’entraînement inclut le package PyTorch et les dépendances associées qui sont nécessaires à l’entraînement sur les processeurs et les GPU.

Envoyez ensuite la tâche PyTorch :
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Entraînement distribué
L’estimateur `PyTorch` vous permet également d’entraîner vos modèles à grande échelle sur les processeurs et clusters GPU des machines virtuelles Azure. Vous pouvez facilement exécuter un entraînement PyTorch distribué avec quelques appels d’API, pendant qu’Azure Machine Learning gère en arrière-plan l’ensemble de l’infrastructure et de l’orchestration qui sont nécessaires pour effectuer ces charges de travail.

Azure Machine Learning prend actuellement en charge l’entraînement distribué MPI de PyTorch avec le framework Horovod.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) est un framework open source ring-allreduce pour l’entraînement distribué, développé par Uber.

Pour exécuter un entraînement PyTorch distribué à l’aide du framework Horovod, créez l’objet PyTorch de la façon suivante :

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

Ce code expose les nouveaux paramètres suivants au constructeur PyTorch :
Paramètre | Description | Default
--|--|--
`node_count` |  Nombre de nœuds à utiliser pour la tâche d’entraînement. | `1`
`process_count_per_node` |  Nombre de processus (ou « workers ») à exécuter sur chaque nœud. | `1`
`distributed_backend` |  Back-end pour lancer l’entraînement distribué, que l’estimateur propose via MPI.  Pour exécuter un entraînement parallèle ou distribué (par exemple, `node_count`> 1 ou `process_count_per_node`> 1, ou les deux) avec MPI (et Horovod), définissez `distributed_backend='mpi'`. L’implémentation MPI utilisée par Azure Machine Learning est [Open MPI](https://www.open-mpi.org/). | `None`

L’exemple ci-dessus exécute l’entraînement distribué avec deux workers, un sur chaque nœud.

Horovod et ses dépendances sont installés automatiquement, ce qui vous permet de les importer dans votre script d’entraînement `train.py` de la façon suivante :
```Python
import torch
import horovod
```

Enfin, envoyez votre tâche PyTorch distribuée :
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>Exemples

Pour les notebooks sur l’apprentissage profond distribué, consultez :
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
