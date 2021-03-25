---
title: Migrer depuis Estimators vers ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Guide pour la migration depuis Estimators vers ScriptRunConfig pour la configuration des tâches de formation.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518870"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migration depuis Estimators vers ScriptRunConfig

Jusqu’à présent, il existait plusieurs méthodes pour configurer une tâche de formation dans Azure Machine Learning via le kit de développement logiciel (SDK), y compris Estimators, ScriptRunConfig et le RunConfiguration de niveau inférieur.   Pour remédier à cette ambiguïté et à cette incohérence, nous simplifions le processus de configuration de la tâche dans Azure ML.  Vous devez maintenant utiliser ScriptRunConfig comme option recommandée pour la configuration des tâches de formation. 

L’utilisation d’Estimators est déconseillée à partir de la version 1.19.0 du kit de développement logiciel (SDK) Python. Vous devez également éviter d’instancier vous-même explicitement un objet RunConfiguration, mais plutôt configurer votre tâche à l’aide de la classe ScriptRunConfig.

Cet article aborde les points à prendre en compte lors de la migration depuis Estimators vers ScriptRunConfig.

> [!IMPORTANT]
> Pour migrer vers ScriptRunConfig depuis Estimators, assurez-vous que vous utilisez au moins la version 1.15.0 du kit de développement logiciel (SDK) Python.

## <a name="scriptrunconfig-documentation-and-samples"></a>Documentation et exemples ScriptRunConfig
La documentation et les exemples Azure Machine Learning ont été mis à jour pour utiliser [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) pour la configuration et la soumission des tâches.

Pour plus d’informations sur l’utilisation de ScriptRunConfig, consultez la documentation suivante :
* [Configurer et soumettre des exécutions d’entraînement](how-to-set-up-training-targets.md)
* [Configuration des exécutions de formation PyTorch](how-to-train-pytorch.md)
* [Configuration des exécutions de formation TensorFlow](how-to-train-tensorflow.md)
* [Configuration des exécutions de formation scikit-learn](how-to-train-scikit-learn.md)

En outre, reportez-vous aux exemples et didacticiels suivants :
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Définition de l’environnement de formation
Bien que les différents estimateurs de l’infrastructure aient des environnements préconfigurés qui sont sauvegardés par des images Docker, les fichiers Dockerfile pour ces images sont privés.  Par conséquent, ces environnements ne sont pas très transparents quant à leur contenu. En outre, les estimateurs tiennent compte des configurations relatives à l’environnement en tant que paramètres individuels (par exemple `pip_packages`, `custom_docker_image`) sur leurs constructeurs respectifs.

Lors de l’utilisation de ScriptRunConfig, toutes les configurations relatives à l’environnement sont encapsulées dans l’objet `Environment` qui est passé dans le paramètre `environment` du constructeur ScriptRunConfig. Pour configurer une tâche de formation, fournissez un environnement qui dispose de toutes les dépendances requises pour votre script de formation. Si aucun environnement n’est fourni, Azure ML utilisera l’une des images de base d’Azure ML, en particulier celle définie par `azureml.core.environment.DEFAULT_CPU_IMAGE`, comme environnement par défaut. Il existe deux façons de fournir un environnement :

* [Utiliser un environnement organisé](how-to-use-environments.md#use-a-curated-environment) : les environnements organisés sont des environnements prédéfinis qui sont disponibles dans votre espace de travail par défaut. Il existe un environnement organisé correspondant à chaque image Docker d’infrastructure/de version associée à chaque estimateur de l’infrastructure.
* [Définir votre propre environnement personnalisé](how-to-use-environments.md)

Voici un exemple d’utilisation de l’environnement organisé PyTorch 1.6 pour la formation :

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Si vous souhaitez spécifier des **variables d’environnement** qui seront définies sur le processus d’exécution du script de formation, utilisez l’objet d’environnement :
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Pour plus d’informations sur la configuration et la gestion des environnements Azure ML, consultez :
* [Comment utiliser des environnements](how-to-use-environments.md)
* [Environnements organisés](resource-curated-environments.md)
* [Effectuer l'apprentissage avec une image Docker personnalisée](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Utilisation des données pour la formation
### <a name="datasets"></a>Groupes de données
Si vous utilisez un jeu de données Azure ML pour la formation, transmettez le jeu de données en tant qu’argument à votre script à l’aide du paramètre `arguments`. En procédant ainsi, vous obtiendrez le chemin de données (point de montage ou chemin de téléchargement) dans votre script de formation via des arguments.

L’exemple suivant configure une tâche de formation où le FileDataset, `mnist_ds`, sera monté sur le calcul distant.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (ancien)
Bien que nous vous recommandions d’utiliser des jeux de données Azure ML plutôt que l’ancienne méthode DataReference, si vous utilisez toujours DataReference pour une raison quelconque, vous devez configurer votre tâche comme suit :
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Pour plus d’informations sur l’utilisation des données pour la formation, consultez :
* [Effectuer l'apprentissage avec des jeux de données dans Azure ML](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Entraînement distribué
Si vous devez configurer une tâche distribuée pour la formation, faites-le en spécifiant le paramètre `distributed_job_config` dans le constructeur ScriptRunConfig. Transmettez un paramètre [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)ou [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) pour les tâches distribuées de ces types respectifs.

L’exemple suivant configure une tâche de formation PyTorch pour utiliser la formation distribuée avec MPI/Horovod :
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Pour plus d'informations, consultez les pages suivantes :
* [Entraînement distribué avec PyTorch](how-to-train-pytorch.md#distributed-training)
* [Entraînement distribué avec TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Divers
Si vous devez accéder à l’objet RunConfiguration sous-jacent pour un ScriptRunConfig pour une raison quelconque, vous pouvez procéder comme suit :
```
src.run_config
```

## <a name="next-steps"></a>Étapes suivantes

* [Configurer et soumettre des exécutions d’entraînement](how-to-set-up-training-targets.md)