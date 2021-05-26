---
title: Entraîner des modèles (créer des travaux) avec l’interface CLI 2.0
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner des modèles (créer des travaux) à l’aide de l’extension Azure CLI pour Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.openlocfilehash: 92397e1648afe8e92cd810827b75cb23c2dac09f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110458273"
---
# <a name="train-models-create-jobs-with-the-20-cli-preview"></a>Entraîner des modèles (créer des travaux) avec l’interface CLI 2.0 (préversion)

L’extension Azure CLI 2.0 pour Machine Learning (préversion) vous permet d’accélérer le processus d’entraînement de modèles tout en effectuant le scale-up et le scale-out au niveau de la capacité de calcul Azure. Par ailleurs, le cycle de vie des modèles fait l’objet d’un suivi et peut être audité.

L’entraînement d’un modèle de machine learning est normalement un processus itératif. Grâce aux outils modernes, l’entraînement des modèles volumineux avec plus de données est plus facile et plus rapide que jamais. Les processus auparavant manuels et fastidieux comme le réglage hyperparamétrique et même la sélection d’algorithmes sont souvent automatisés. Avec l’interface CLI d’Azure Machine Learning, vous pouvez assurer le suivi de vos travaux (et modèles) dans un [espace de travail](concept-workspace.md) avec des balayages hyperparamétriques, un scale-up au niveau des capacités de calcul Azure hautes performances et un scale-out utilisant un entraînement distribué.

> [!TIP]
> Pour profiter d’un environnement de développement complet, utilisez Visual Studio Code et l’[extension Azure Machine Learning](how-to-setup-vs-code.md) pour [gérer les ressources Azure Machine Learning](how-to-manage-resources-vscode.md) et [entraîner des modèles de machine learning](tutorial-train-deploy-image-classification-model-vscode.md).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

- Pour utiliser l'interface de ligne de commande, vous devez disposer d'un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.
- [Installer et configurer l’extension Azure CLI pour Machine Learning](how-to-configure-cli.md)
- Clonez le référentiel d’exemples :

    ```azurecli-interactive
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli
    ```

## <a name="introducing-jobs"></a>Introduction aux travaux

Pour l’interface CLI d’Azure Machine Learning, les travaux sont créés au format YAML. Un travail englobe les aspects suivants :

- Quoi exécuter
- Comment l’exécuter
- Où l’exécuter

Le travail « hello world » comporte les trois :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/hello-world.yml":::

Il s’agit simplement d’un exemple de travail qui ne génère rien d’autre qu’une ligne dans le fichier journal. En plus des journaux générés par le système, il est généralement souhaitable de générer des artefacts supplémentaires, comme des binaires de modèle et les métadonnées associées.

Azure Machine Learning capture automatiquement les artefacts suivants :

- Les répertoires `./outputs` et `./logs` font l’objet d’un traitement spécial de la part d’Azure Machine Learning. Si vous écrivez des fichiers dans ces répertoires pendant le travail, ces fichiers sont chargés dans l’historique des exécutions du travail, ce qui vous permet d’y accéder toujours une fois le travail terminé. Le dossier `./outputs` est chargé à la fin du travail, tandis que les fichiers écrits dans `./logs` sont chargés en temps réel. Vous pouvez utiliser ce dernier si vous souhaitez diffuser des journaux en continu pendant le travail, tels que des journaux TensorBoard.
- Azure Machine Learning s’intègre avec la fonctionnalité de suivi de MLflow. Vous pouvez utiliser `mlflow.autolog()` pour plusieurs infrastructures de ML courantes afin de journaliser les paramètres de modèle, les métriques de performances, les artefacts de modèle et même des graphiques d’importance des caractéristiques. Vous pouvez aussi utiliser les méthodes `mlflow.log_*()` pour journaliser explicitement les paramètres, les métriques et les artefacts. L’ensemble des métriques et des artefacts journalisés par MLflow sont enregistrés dans l’historique des exécutions du travail.

Souvent, un travail implique d’exécuter du code source qui est modifié et contrôlé localement. Vous pouvez spécifier le répertoire de code source à inclure dans le travail, à partir duquel la commande sera exécutée.

Par exemple, examinez le répertoire du projet `jobs/train/lightgbm/iris` dans le référentiel d’exemples :

```tree
.
├── environment.yml
├── job-sweep.yml
├── job.yml
└── src
    └── main.py
```

Ce répertoire contient deux fichiers de travail, un fichier d’environnement conda et un sous-répertoire de code source `src`. Même si cet exemple comporte un seul fichier sous `src`, le sous-répertoire entier est chargé de manière récursive et peut être utilisé dans le travail.

Le travail à la commande de base est configuré via `job.yml` :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

Ce travail peut être créé et exécuté via `az ml job create` en utilisant le paramètre `--file/-f`. Cependant, le travail cible une capacité de calcul nommée `cpu-cluster` qui n’existe pas encore. Pour exécuter le travail d’abord en local, vous pouvez remplacer la cible de calcul par `--set` :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="lightgbm_iris_local":::

Même si l’exécution locale de ce travail est plus lente que l’exécution de `python main.py` dans un environnement Python local avec les packages nécessaires, le premier vous permet de :

- Enregistrer l’historique des exécutions dans le studio Azure Machine Learning
- Reproduire l’exécution sur des cibles de calcul distantes (scale-up, scale-out, balayage hyperparamétrique)
- Suivre les détails de l’envoi de l’exécution, notamment le commit et le dépôt git du code source
- Suivre les métriques, les métadonnées et les artefacts du modèle
- Éviter l’installation et la gestion de packages dans votre environnement local

> [!IMPORTANT]
> [Docker](https://docker.io) doit être installé et s’exécuter localement. Python doit être installé dans l’environnement du travail. Pour les exécutions locales qui utilisent `inputs`, le package Python `azureml-dataprep` doit être installé dans l’environnement du travail.

> [!TIP]
> Plusieurs minutes sont nécessaire pour extraire l’image Docker de base et créer l’environnement conda par-dessus. Utilisez des images Docker prédéfinies pour éviter le temps de génération d’image.

## <a name="create-compute"></a>Créer une capacité de calcul

Vous pouvez créer un cluster de calcul Azure Machine Learning à partir de la ligne de commande. Par exemple, les commandes suivantes créent un cluster nommé `cpu-cluster` et un autre nommé `gpu-cluster`.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="create_computes":::

Notez que le calcul ne vous est pas facturé à ce stade, car `cpu-cluster` et `gpu-cluster` restent aux nœuds 0 tant qu’aucun travail n’est envoyé. Apprenez-en davantage sur la [planification et la gestion des coûts pour AmlCompute](concept-plan-manage-cost.md#use-azure-machine-learning-compute-cluster-amlcompute).

Utilisez `az ml compute create -h` pour plus d’informations sur les options de création de capacités de calcul.

## <a name="basic-python-training-job"></a>Travail d’entraînement Python de base

Une fois `cpu-cluster` créé, vous pouvez exécuter le travail d’entraînement de base, qui génère un modèle et les métadonnées associées. Examinons de près le fichier YAML du travail :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

| Clé | Description |
| --- | ----------- |
| `$schema` | [Facultatif] Schéma YAML. Vous pouvez afficher le [schéma](https://azuremlschemas.azureedge.net/latest/commandJob.schema.json) de l’exemple ci-dessus dans un navigateur pour voir toutes les options disponibles pour un fichier YAML de travail de commande. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, y compris cette propriété `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. |
| `code.local_path` | [Facultatif] Chemin local du répertoire source (par rapport au fichier YAML) à charger et à utiliser avec le travail. Envisagez d’utiliser `src` dans le même répertoire que le ou les fichiers de travail dans un souci de cohérence. |
| `command` | Commande à exécuter. La convention `>` permet de créer des commandes multilignes lisibles en pliant les nouvelles lignes en espaces. Les arguments de ligne de commande peuvent être écrits explicitement dans la commande ou déduits à partir d’autres sections, en particulier `inputs` ou `search_space`, en utilisant une notation d’accolades. |
| `inputs` | [Facultatif] Dictionnaire des liaisons de données d’entrée, où la clé est un nom que vous spécifiez pour la liaison d’entrée. La valeur de chaque élément est la liaison d’entrée, qui se compose des champs `data` et `mode`. `data` peut être soit 1) une référence à une ressource de données Azure Machine Learning versionnée existante utilisant le préfixe `azureml:` (par exemple `azureml:iris-url:1` pour pointer vers la version 1 d’une ressource de données nommée « iris-url ») soit 2) une définition inline des données. Utilisez `data.path` pour spécifier un emplacement cloud. Utilisez `data.local_path` pour spécifier les données du système de fichiers local qui seront chargées dans le magasin de données par défaut. `mode` indique comment vous voulez rendre les données accessibles au calcul pour le travail. « mount » et « download » sont les deux options prises en charge. <br><br> Il est possible de faire référence à une entrée dans la commande par son nom, par exemple `{inputs.my_input_name}`. Azure Machine Learning résout alors cette notation paramétrisée dans la commande comme étant l’emplacement de ces données sur la cible de calcul pendant l’exécution. Par exemple, si les données sont configurées pour être montées, `{inputs.my_input_name}` la résout comme étant le point de montage. |
| `environment` | Environnement d’exécution de la commande sur la cible de calcul. Vous pouvez définir l’environnement inline en spécifiant l’image Docker à utiliser ou le Dockerfile pour générer l’image. Vous pouvez aussi faire référence à un environnement versionné existant dans l’espace de travail ou à l’un des environnements organisés d’Azure ML en utilisant le préfixe `azureml:`. Par exemple, `azureml:AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36:1` fait référence à la version 1 d’un environnement organisé pour TensorFlow avec prise en charge de GPU. <br><br> Python doit être installé dans l’environnement utilisé pour l’entraînement. Exécutez `apt-get update -y && apt-get install python3 -y` dans votre Dockerfile pour l’installation, si nécessaire. |
| `compute.target` | La cible de calcul. Spécifiez `local` pour une exécution locale ou utilisez le préfixe `azureml:` pour référencer une ressource de calcul existante dans votre espace de travail. Par exemple, `azureml:cpu-cluster` pointe vers une cible de calcul nommée « cpu-cluster ». |
| `experiment_name` | [Facultatif] Étiquette le travail pour une meilleure organisation dans le studio Azure Machine Learning. Chaque enregistrement d’exécution du travail est organisé au titre de l’expérience correspondante dans l’onglet « Experiment » (Expérience) du studio. En cas d’omission, il prend par défaut le nom du répertoire de travail dans lequel le travail a été créé. |
| `name` | [Facultatif] Nom du travail, qui doit être unique parmi l’ensemble des travaux figurant dans un espace de travail. Si aucun nom n’est spécifié dans le fichier YAML via le champ `name` ou dans la ligne de commande via `--name/-n`, un GUID/UUID est automatiquement généré et utilisé pour le nom. Le nom du travail correspond à l’ID d’exécution dans l’interface utilisateur du studio de l’enregistrement d’exécution du travail. |

La création de ce travail a pour effet de charger les ressources locales spécifiées, comme le répertoire du code source, de valider le fichier YAML et d’envoyer l’exécution. Si nécessaire, l’environnement est généré, le capacité de calcul fait l’objet d’un scale-up et est configurée pour exécuter le travail.

Pour exécuter le travail d’entraînement lightgbm/iris :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="lightgbm_iris":::

Une fois le travail terminé, vous pouvez télécharger les sorties :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="download_outputs":::

> [!IMPORTANT]
> Remplacez `$run_id` par votre ID d’exécution, qui se trouve dans la sortie de la console ou dans la page des détails de l’exécution du studio.

Cela a pour effet de télécharger les journaux et les artefacts éventuellement capturés en local dans un répertoire nommé `$run_id`. Pour cet exemple, le sous-répertoire de modèles journalisé par MLflow est téléchargé.

## <a name="sweep-hyperparameters"></a>Balayer les hyperparamètres

Azure Machine Learning vous permet également de régler plus efficacement les hyperparamètres pour vos modèles de machine learning. Vous pouvez configurer un travail de réglage hyperparamétrique, appelé travail de balayage, et l’envoyer via l’interface CLI. Pour plus d’informations sur l’offre de réglage hyperparamétrique d’Azure Machine Learning, consultez [Hyperparamètres réglant un modèle](how-to-tune-hyperparameters.md).

Vous pouvez changer `job.yml` en `job-sweep.yml` pour balayer les hyperparamètres :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job-sweep.yml":::

| Clé | Description |
| --- | ----------- |
| `$schema` | [Facultatif] Schéma YAML, qui a changé et qui pointe désormais vers le [schéma](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json) du travail de balayage. |
| `type` | Type de travail. |
| `algorithm` | L’algorithme d’échantillonnage - "random" est souvent un bon choix. Reportez-vous au schéma pour voir l’énumération des options. |
| `trial` | Configuration du travail de commande pour chaque essai à exécuter. La commande (`trial.command`) de l’exemple précédent a été modifiée pour utiliser la notation `{search_space.<hyperparameter_name>}` afin de référencer les hyperparamètres définis dans `search_space`. Azure Machine Learning résout ensuite chaque notation paramétrisée comme étant la valeur de l’hyperparamètre correspondant qu’il génère pour chaque essai. |
| `search_space` | Dictionnaire des hyperparamètres à balayer. La clé est un nom pour l’hyperparamètre, par exemple `search_space.learning_rate`. Notez que le nom ne doit pas nécessairement correspondre à l’argument du script d’entraînement proprement dit. Il doit simplement correspondre à la référence de l’espace de recherche notée avec des accolades dans la commande, par exemple `{search_space.learning_rate}`. La valeur est la distribution hyperparamétrique. Reportez-vous au schéma pour voir l’énumération des options. |
| `objective.primary_metric` | Métrique d’optimisation, qui doit correspondre au nom d’une métrique journalisée à partir du code d’entraînement. `objective.goal` spécifie la direction ("minimize"/"maximize"). Reportez-vous au schéma pour voir l’énumération complète des options. |
| `max_total_trials` | Nombre maximal d’essais individuels à exécuter. |
| `max_concurrent_trials` | [Facultatif] Nombre maximal d’essais à exécuter simultanément sur votre cluster de calcul. |
| `timeout_minutes` | [Facultatif] Nombre maximal de minutes durant lesquelles le travail de balayage doit être exécuté. |
| `experiment_name` | [Facultatif] Expérience au titre de laquelle le travail de balayage doit être suivi. En cas d’omission, il s’agit par défaut du nom du répertoire de travail au moment où le travail est créé. |

Créez le travail et ouvrez-le dans le studio :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="lightgbm_iris_sweep":::

> [!TIP]
> Les balayages hyperparamétriques peuvent être utilisés avec des travaux de commande distribués.

## <a name="distributed-training"></a>Entraînement distribué

Vous pouvez spécifier la section `distributed` dans un travail de commande. Azure ML prend en charge l’entraînement distribué pour PyTorch, TensorFlow et les infrastructures compatibles MPI. PyTorch et TensorFlow permettent un entraînement distribué natif pour leurs infrastructures respectives, comme les API `tf.distributed.Strategy` pour TensorFlow.

Veillez à définir `compute.instance_count`, dont la valeur par défaut est 1, en fonction du nombre de nœuds que vous voulez pour le travail.

### <a name="pytorch"></a>PyTorch

Exemple de fichier YAML pour l’entraînement PyTorch distribué sur le jeu de données CIFAR-10 :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/pytorch/cifar-distributed/job.yml":::

Notez qu’il fait référence à des données locales, qui ne sont pas présentes dans le référentiel d’exemples cloné. Vous devez commencer par télécharger, extraire et déplacer le jeu de données CIFAR-10 localement, en le plaçant à l’emplacement approprié dans le répertoire du projet :

:::code language="bash" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="download_cifar":::

Créez le travail et ouvrez-le dans le studio :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="pytorch_cifar":::

### <a name="tensorflow"></a>TensorFlow

Exemple de fichier YAML pour l’entraînement TensorFlow distribué sur le jeu de données MNIST :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed/job.yml":::

Créez le travail et ouvrez-le dans le studio :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="tensorflow_mnist":::

### <a name="mpi"></a>MPI

Azure ML prend en charge le lancement d’un travail MPI sur plusieurs nœuds et avec plusieurs processus par nœud. Le travail est lancé via `mpirun`. Par exemple, si votre code d’entraînement utilise l’infrastructure Horovod pour l’entraînement distribué, vous pouvez exploiter ce type de travail pour effectuer un entraînement sur Azure ML.

Pour lancer un travail MPI, spécifiez `mpi` comme type et indiquez le nombre de processus par nœud à lancer (`process_count_per_instance`) dans la section `distribution`. Si ce champ n’est pas spécifié, par défaut, Azure ML lance un processus par nœud.

Exemple de spécification YAML, qui exécute un travail TensorFlow sur MNIST en utilisant Horovod :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed-horovod/job.yml":::

Créez le travail et ouvrez-le dans le studio :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-train-cli.sh" id="tensorflow_mnist_horovod":::

## <a name="next-steps"></a>Étapes suivantes

- [Déployer et scorer un modèle de machine learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md)
- [Entraîner des modèles avec REST (préversion)](how-to-train-with-rest.md)
