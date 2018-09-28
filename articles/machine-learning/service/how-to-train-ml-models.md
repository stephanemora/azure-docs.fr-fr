---
title: Entraîner des modèles Machine Learning avec Azure Machine Learning
description: Découvrez comment effectuer un entraînement à nœud unique et un entraînement distribué sur des modèles Machine Learning traditionnels et d’apprentissage profond avec les services Azure Machine Learning
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983580"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Comment entraîner des modèles avec Azure Machine Learning

L’entraînement de modèles Machine Learning (plus particulièrement des réseaux neuronaux profonds) est une tâche longue et nécessitant beaucoup de ressources système. Quand vous aurez terminé d’écrire votre script d’entraînement et que vous l’aurez exécuté sur un petit sous-ensemble de données sur votre ordinateur local, vous voudrez probablement mettre à l’échelle votre charge de travail.

Pour faciliter l’entraînement, le SDK Python de Azure Machine Learning fournit une abstraction de haut niveau, la classe Estimator, qui permet aux utilisateurs d’entraîner facilement leurs modèles dans l’écosystème Azure. Vous pouvez créer et utiliser un objet Estimator pour envoyer le code d’entraînement que vous souhaitez exécuter sur la cible de calcul distante, qu’il s’agisse d’un entraînement à nœud unique ou d’un entraînement distribué sur un cluster GPU. Pour les tâches PyTorch et TensorFlow, Azure Machine Learning fournit également des estimateurs personnalisés PyTorch et TensorFlow qui facilitent l’utilisation de ces frameworks.

## <a name="train-with-an-estimator"></a>Entraîner des modèles avec un estimateur

Quand vous avez créé votre [espace de travail](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace) et configuré votre [environnement de développement](how-to-configure-environment.md), vous devez effectuer les étapes suivantes pour entraîner un modèle dans Azure Machine Learning :  
1. [Créer une cible de calcul distante](how-to-set-up-training-targets.md)
2. [Charger vos données d’entraînement](how-to-access-data.md) (facultatif)
3. Créer votre script d’entraînement
4. Créer un objet Estimator
5. Envoyer votre tâche d’entraînement

Cet article concerne les étapes 4 et 5. Pour les étapes 1 à 3, reportez-vous à ce [tutoriel](tutorial-train-models-with-aml.md) pour obtenir un exemple.

### <a name="single-node-training"></a>Entraînement à nœud unique

Le code suivant décrit un entraînement à nœud unique exécuté sur une cible de calcul distante dans Azure, pour un modèle scikit-learn. Vous devez déjà avoir créé votre objet [Cible de calcul](how-to-set-up-training-targets.md#batch) `compute_target`, ainsi que votre objet [Magasin de données](how-to-access-data.md) `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

L’extrait de code ci-dessus spécifie les paramètres suivants au constructeur Estimator :
* `source_directory` : répertoire local qui contient l’ensemble du code nécessaire à la tâche d’entraînement. Ce dossier est copié de votre ordinateur local vers la cible de calcul distante. 
* `script_params` : dictionnaire spécifiant les arguments de ligne de commande de votre script d’entraînement `entry_script`, sous la forme de paires <argument de ligne de commande, valeur>
* `compute_target` : cible de calcul distante sur laquelle votre script d’entraînement s’exécute, dans ce cas un cluster [Batch AI](how-to-set-up-training-targets.md#batch)
* `entry_script` : chemin (relatif à `source_directory`) du script d’entraînement à exécuter sur le calcul à distance. Ce fichier et tous les autres fichiers dont il dépend, doivent se trouver dans ce dossier.
* `conda_packages` : liste des packages Python à installer via Conda et dont a besoin votre script d’entraînement.  
Le constructeur a un autre paramètre appelé `pip_packages` que vous pouvez utiliser pour tous les packages pip nécessaires.

Maintenant que vous avez créé votre objet Estimator, vous pouvez soumettre la tâche d’entraînement à exécuter sur le calcul à distance via un appel à la fonction `submit` sur votre objet [Expérience](concept-azure-machine-learning-architecture.md#experiment) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Dossiers spéciaux** : deux dossiers, *outputs* et *logs*, reçoivent un traitement spécial de la part d’Azure Machine Learning. Pendant l’entraînement, si vous écrivez des fichiers dans les dossiers *outputs* et *logs* relatifs au répertoire racine (`./outputs` et `./logs`, respectivement), ces fichiers seront automatiquement chargés dans votre historique des exécutions. Vous pourrez donc y accéder une fois l’exécution terminée. 
>
> Pour accéder aux artefacts créés pendant l’entraînement (par exemple, les fichiers de modèles, les points de contrôle, les fichiers de données ou les images tracées), écrivez-les dans le dossier `./outputs`.
>
> De même, vous pouvez écrire tous les journaux de votre exécution d’entraînement dans le dossier `./logs`. Pour utiliser [l’intégration TensorBoard](https://aka.ms/aml-notebook-tb) d’Azure Machine Learning, écrivez vos journaux TensorBoard dans ce dossier. Lorsque votre exécution est en cours, vous pouvez lancer TensorBoard et diffuser ces journaux en continu.  Plus tard, vous pourrez également restaurer les journaux à partir de l’une de vos exécutions précédentes.
>
> Par exemple, pour télécharger sur votre ordinateur local un fichier écrit dans le dossier *outputs* après votre exécution d’entraînement distant : `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Entraînement distribué et images Docker personnalisées

Il existe deux autres scénarios d’entraînement que vous pouvez utiliser avec l’estimateur :
1. Utilisation d’une image Docker personnalisée
2. Entraînement distribué sur un cluster à plusieurs nœuds

Le code suivant montre comment procéder à l’entraînement distribué pour un modèle CNTK. En outre, au lieu d’utiliser les images Azure Machine Learning par défaut, il part du principe que vous avez votre propre image Docker personnalisée pour l’entraînement.

Vous devez déjà avoir créé votre objet de [cible de calcul](how-to-set-up-training-targets.md#batch) `compute_target`. Vous pouvez créer l’estimateur de la façon suivante :

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

Le code ci-dessus expose les nouveaux paramètres suivants au constructeur Estimator :
* `custom_docker_base_image` : nom de l’image que vous souhaitez utiliser. Vous pouvez uniquement fournir des images disponibles dans les référentiels Docker publics (dans ce cas, Docker Hub). Pour utiliser une image à partir d’un référentiel Docker privé, utilisez le paramètre `environment_definition` du constructeur.
* `node_count` : nombre de nœuds à utiliser pour la tâche d’entraînement. Par défaut, cet argument a la valeur `1`.
* `process_count_per_node` : nombre de processus (ou « workers ») à exécuter sur chaque nœud. Dans ce cas, utilisez les GPU `2` disponibles sur chaque nœud. Par défaut, cet argument a la valeur `1`.
* `distributed_backend` : serveur backend pour lancer l’entraînement distribué, que l’estimateur propose via MPI. Par défaut, cet argument a la valeur `None`. Si vous souhaitez exécuter un entraînement parallèle ou distribué (par exemple, `node_count`> 1 ou `process_count_per_node`> 1, ou les deux), affectez la valeur `distributed_backend='mpi'`. L’implémentation MPI utilisée par AML est [Open MPI](https://www.open-mpi.org/).

Enfin, envoyez la tâche d’entraînement :
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Exemples
Pour obtenir un tutoriel sur l’entraînement d’un modèle sklearn, consultez :
* `tutorials/01.train-models.ipynb`

Pour obtenir un tutoriel sur l’entraînement distribué CNTK à l’aide d’un Docker personnalisé, consultez :
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

Consultez ces blocs-notes :

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Entraîner des modèles PyTorch](how-to-train-pytorch.md)
* [Entraîner des modèles TensorFlow](how-to-train-tensorflow.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
