---
title: 'Tutoriel :  Prise en main de Machine Learning - Python'
titleSuffix: Azure Machine Learning
description: Dans ce didacticiel, vous allez vous familiariser avec le Kit de développement logiciel (SDK) Python d’Azure Machine Learning s’exécutant dans votre environnement de développement personnel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929332"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Tutoriel : Prise en main d’Azure Machine Learning dans votre environnement de développement (partie 1 sur 4)

Dans cette **série de didacticiels en quatre parties**, vous apprendrez les notions de base d’Azure Machine Learning et exécuterez des tâches Python de ML basées sur des travaux dans le cloud Azure, notamment :

1. Configurer un espace de travail et votre environnement de développement Machine Learning local.
2. Exécuter du code dans le cloud à l’aide du kit de développement logiciel (SDK) Python d’Azure Machine Learning.
3. Gérer l’environnement Python que vous utilisez pour entraîner le modèle.
4. Charger des données dans Azure et les utiliser dans la formation.

Dans la **partie 1 de cette série de didacticiels**, vous allez :

> [!div class="checklist"]
> * Installer le Kit de développement logiciel (SDK) Azure Machine Learning
> * Configurer la structure de répertoire pour le code
> * Création d’un espace de travail Microsoft Azure Machine Learning
> * Configurer votre environnement de développement local
> * Configurer un cluster de calcul

>[!NOTE]
> Cette série de didacticiels met l’accent sur les concepts d’Azure Machine Learning adaptés aux tâches de Machine Learning __basées sur les travaux__ de Python qui sont gourmandes en ressources de calcul et/ou nécessitent une reproductibilité. Si vos tâches de Machine Learning ne correspondent pas à ce profil, utilisez la [fonctionnalité Jupyter ou RStudio sur une instance de calcul Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md) à intégrer à Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez [Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.
- Familiarisez-vous avec Python et les [concepts de Machine Learning](concept-azure-machine-learning-architecture.md). Par exemple, les environnements, les formations, le scoring, etc.
- Un environnement de développement local : un ordinateur portable sur lequel Python est installé et votre IDE favori (par exemple : VSCode, Pycharm, Jupyter, etc.).

## <a name="install-the-azure-machine-learning-sdk"></a>Installer le Kit de développement logiciel (SDK) Azure Machine Learning

Dans ce didacticiel, nous utilisons le Kit de développement logiciel (SDK) Python d’Azure Machine Learning.

Vous pouvez utiliser les outils que vous connaissez le mieux, par exemple Conda, PIP, etc. pour configurer un environnement à utiliser dans ce didacticiel. Installation dans l’environnement du Kit de développement logiciel (SDK) Python d’Azure Machine Learning via PIP :

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Créer la structure de répertoire pour le code
Pour ce didacticiel, nous vous recommandons d’utiliser la configuration de répertoire simple suivante :

```markdown
tutorial
└──.azureml
```

- **didacticiel** (répertoire de niveau supérieur du projet)
- **.azureml** (sous-répertoire masqué du didacticiel) :  Le répertoire `.azureml` permet de stocker les fichiers de configuration Azure Machine Learning.

## <a name="create-an-azure-machine-learning-workspace"></a>Créer un espace de travail Azure Machine Learning

Un espace de travail est la ressource de niveau supérieur pour Azure Machine Learning et est un endroit centralisé où vous pouvez :

- gérer les ressources, telles que celles de calcul ;
- stocker des ressources, telles que des blocs-notes, des environnements, des jeux de données, des pipelines, des modèles, des points de terminaison, etc. ;
- collaborer avec d'autres membres de l’équipe.

Dans le répertoire parent de niveau supérieur (`tutorial`), ajoutez un nouveau fichier Python nommé `01-create-workspace.py` avec le code ci-dessous. Adaptez les paramètres (nom, ID d’abonnement, groupe de ressources et [emplacement](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) selon vos préférences.

Vous pouvez exécuter le code dans une session interactive ou sous la forme d’un fichier Python.

>[!NOTE]
> Lorsque vous utilisez un environnement de développement local (par exemple, un ordinateur portable), vous êtes invité à vous authentifier auprès de votre espace de travail à l’aide d’un *code d’appareil* la première fois que vous exécutez le code ci-dessous. Suivez les instructions à l’écran.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Exécutez ce code à partir du répertoire `tutorial` :

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

Une fois l’extrait de code ci-dessus exécuté, votre structure de dossiers se présente comme suit :

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Le fichier `.azureml/config.json` contient les métadonnées nécessaires pour se connecter à votre espace de travail Azure Machine Learning, à savoir votre ID d’abonnement, le groupe de ressources et le nom de l’espace de travail. 

> [!NOTE]
> Le contenu de `config.json` n’est pas secret : ces détails peuvent tout à fait être partagés.
> L’authentification est toujours requise pour interagir avec votre espace de travail Azure Machine Learning.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Créer un cluster de calcul Azure Machine Learning

Créez un script Python dans le répertoire de niveau supérieur `tutorial` nommé `02-create-compute.py` et indiquez le code suivant pour créer un cluster de calcul Azure Machine Learning qui sera mis à l’échelle automatiquement entre zéro et quatre nœuds :

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Exécuter le fichier Python :

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Une fois le cluster créé, aucun nœud n’est configuré. Par conséquent, le cluster n’implique **aucun** coût tant que vous n’avez pas envoyé de travail. Ce cluster connaîtra un scale-down lorsqu’il restera inactif pendant 2 400 secondes (40 minutes).

Votre structure de dossiers se présente désormais comme suit :

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel de configuration, vous disposez des éléments suivants :

- un espace de travail Azure Machine Learning créé ;
- Configurer votre environnement de développement local
- un cluster de calcul Azure Machine Learning créé.

Dans le didacticiel suivant, vous passerez en revue l’envoi d’un script au cluster de calcul Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutoriel : Exécuter un script Python « Hello World » dans Azure](tutorial-1st-experiment-hello-world.md)
