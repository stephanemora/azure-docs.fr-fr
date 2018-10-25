---
title: Entraîner des modèles Machine Learning à l’aide d’une classe Estimator avec Azure Machine Learning
description: Découvrir comment effectuer un entraînement à nœud unique et un entraînement distribué sur des modèles Machine Learning traditionnels et d’apprentissage profond en utilisant la classe Estimator des services Azure Machine Learning
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: c47761c184d0e6c091ff49b3eca2fdf89574b49d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114857"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>Comment entraîner des modèles avec Azure Machine Learning

L’entraînement de modèles Machine Learning (plus particulièrement des réseaux neuronaux profonds) est une tâche longue et nécessitant beaucoup de ressources système. Quand vous aurez terminé d’écrire votre script d’entraînement et que vous l’aurez exécuté sur un petit sous-ensemble de données sur votre ordinateur local, vous voudrez probablement mettre à l’échelle votre charge de travail.

Pour faciliter l’entraînement, le kit SDK Python d’Azure Machine Learning propose une abstraction de haut niveau, la classe estimator, qui permet aux utilisateurs d’entraîner facilement leurs modèles dans l’écosystème Azure. Vous pouvez créer et utiliser un objet `Estimator` pour soumettre le code d’entraînement que vous souhaitez exécuter sur la cible de calcul distante, qu’il s’agisse d’un entraînement à nœud unique ou d’un entraînement distribué sur un cluster GPU. Pour les tâches PyTorch et TensorFlow, Azure Machine Learning fournit aussi les estimateurs personnalisés `PyTorch` et `TensorFlow` (respectivement), qui simplifient l’utilisation de ces frameworks.

## <a name="train-with-an-estimator"></a>Entraîner avec un estimateur

Une fois que vous avez créé votre [espace de travail](concept-azure-machine-learning-architecture.md#workspace) et configuré votre [environnement de développement](how-to-configure-environment.md), l’entraînement d’un modèle dans Azure Machine Learning passe par les étapes suivantes :  
1. Créer une [cible de calcul distante](how-to-set-up-training-targets.md)
2. Charger vos [données d’entraînement](how-to-access-data.md) (facultatif)
3. Créer votre [script d’entraînement](tutorial-train-models-with-aml.md#create-a-training-script)
4. Créer un objet `Estimator`
5. Envoyer votre tâche d’entraînement

Cet article concerne les étapes 4 et 5. Pour les étapes 1 à 3, reportez-vous au [tutoriel Entraîner un modèle](tutorial-train-models-with-aml.md) pour obtenir un exemple.

### <a name="single-node-training"></a>Entraînement à nœud unique

Utilisez `Estimator` pour un entraînement à nœud unique exécuté sur une cible de calcul distante dans Azure pour un modèle scikit-learn. Vous devez déjà avoir créé votre objet [cible de calcul](how-to-set-up-training-targets.md#batch) `compute_target`, ainsi que votre objet [banque de données ](how-to-access-data.md) `ds`.

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

Cet extrait de code spécifie les paramètres suivants au constructeur `Estimator`.

Paramètre | Description
--|--
`source_directory`| Répertoire local qui contient l’ensemble du code nécessaire à la tâche d’entraînement. Ce dossier est copié de votre ordinateur local vers la cible de calcul distante 
`script_params`| Dictionnaire spécifiant les arguments de ligne de commande de votre script d’entraînement `entry_script`, sous la forme de paires <argument de ligne de commande, valeur>
`compute_target`| Cible de calcul distante sur laquelle votre script d’entraînement s’exécute, dans ce cas un cluster [Batch AI](how-to-set-up-training-targets.md#batch)
`entry_script`| Chemin de fichier (relatif à `source_directory`) du script d’entraînement à exécuter sur la cible de calcul distante. Ce fichier et tous les autres fichiers dont il dépend doivent se trouver dans ce dossier
`conda_packages`| Liste des packages Python à installer via conda et dont a besoin votre script d’entraînement.  
Le constructeur possède un autre paramètre appelé `pip_packages` que vous pouvez utiliser pour tous les packages pip nécessaires

Maintenant que vous avez créé votre objet `Estimator`, soumettez la tâche d’entraînement à exécuter sur la cible de calcul à distance avec un appel à la fonction `submit` sur votre objet [Expérience](concept-azure-machine-learning-architecture.md#experiment) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **Dossiers spéciaux** : deux dossiers, *outputs* et *logs*, reçoivent un traitement spécial de la part d’Azure Machine Learning. Pendant l’entraînement, quand vous écrivez des fichiers dans les dossiers *outputs* et *logs* relatifs au répertoire racine (`./outputs` et `./logs`, respectivement), les fichiers sont automatiquement chargés dans votre historique des exécutions. Vous pouvez ainsi y accéder une fois l’exécution terminée.
>
> Pour créer des artefacts pendant l’entraînement (par exemple, des fichiers de modèle, des points de contrôle, des fichiers de données ou des images tracées), écrivez-les dans le dossier `./outputs`.
>
> De même, écrivez les journaux de votre exécution d’entraînement dans le dossier `./logs`. Pour utiliser [l’intégration TensorBoard](https://aka.ms/aml-notebook-tb) d’Azure Machine Learning, écrivez vos journaux TensorBoard dans ce dossier. Lorsque votre exécution est en cours, vous pouvez lancer TensorBoard et diffuser ces journaux en continu.  Plus tard, vous pourrez également restaurer les journaux à partir de l’une de vos exécutions précédentes.
>
> Par exemple, pour télécharger sur votre ordinateur local un fichier écrit dans le dossier *outputs* après votre exécution d’entraînement distant : `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Entraînement distribué et images Docker personnalisées

Il existe deux autres scénarios d’entraînement que vous pouvez mener à bien avec `Estimator` :
* Utilisation d’une image Docker personnalisée
* Entraînement distribué sur un cluster à plusieurs nœuds

Le code suivant montre comment procéder à l’entraînement distribué pour un modèle CNTK. Par ailleurs, au lieu d’utiliser les images Azure Machine Learning par défaut, il part du principe que vous utilisez votre propre image Docker personnalisée pour l’entraînement.

Vous devez déjà avoir créé votre objet de [cible de calcul](how-to-set-up-training-targets.md#batch) `compute_target`. Pour créer l’estimateur, procédez comme suit :

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

Le code ci-dessus expose les nouveaux paramètres suivants au constructeur `Estimator` :

Paramètre | Description | Valeur par défaut
--|--|--
`custom_docker_base_image`| Nom de l’image que vous voulez utiliser. Indiquez uniquement des images disponibles dans les référentiels Docker publics (dans ce cas, Docker Hub). Pour utiliser une image à partir d’un référentiel Docker privé, utilisez le paramètre `environment_definition` du constructeur. | `None`
`node_count`| Nombre de nœuds à utiliser pour la tâche d’entraînement. | `1`
`process_count_per_node`| Nombre de processus (ou « workers ») à exécuter sur chaque nœud. Dans ce cas, utilisez les GPU `2` disponibles sur chaque nœud.| `1`
`distributed_backend`| Back-end pour lancer l’entraînement distribué, que l’estimateur propose via MPI.  Pour mener à bien un entraînement parallèle ou distribué (par exemple, `node_count`> 1 ou `process_count_per_node`> 1, ou les deux), définissez `distributed_backend='mpi'`. L’implémentation MPI utilisée par AML est [Open MPI](https://www.open-mpi.org/).| `None`

Enfin, envoyez la tâche d’entraînement :
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>Exemples
Pour obtenir un tutoriel sur l’entraînement d’un modèle sklearn, consultez :
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

Pour obtenir un tutoriel sur l’entraînement distribué CNTK à l’aide d’un Docker personnalisé, consultez :
* [training/06.distributed-cntk-with-custom-docker](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/06.distributed-cntk-with-custom-docker)

Consultez ces notebooks :

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)
* [Entraîner des modèles PyTorch](how-to-train-pytorch.md)
* [Entraîner des modèles TensorFlow](how-to-train-tensorflow.md)
* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
* [Déployer un modèle entraîné](how-to-deploy-and-where.md)
