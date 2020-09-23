---
title: 'Tutoriel : Exécuter un script Python « Hello World »'
titleSuffix: Azure Machine Learning
description: La deuxième partie de notre série de tutoriels sur la prise en main d’Azure ML explique comment soumettre un script Python « Hello World » simple dans le cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 98f4c30d03763e070b1bdc32a5e6e099556916ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929333"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Tutoriel : Exécuter un script Python « Hello World » (partie 2 sur 4)

Dans ce tutoriel, vous allez apprendre à utiliser le Kit de développement logiciel (SDK) Azure Machine Learning Python pour soumettre et exécuter un script Python « Hello World ».

Ce tutoriel constitue la **deuxième partie d’une série de quatre tutoriels** conçus pour vous apprendre les principes fondamentaux de Azure Machine Learning à travers des exercices Machine Learning basés sur des tâches Azure. Ce tutoriel s’appuie sur le travail que vous avez effectué dans le premier tutoriel : [Configurer votre ordinateur local pour Azure Machine Learning](
tutorial-1st-experiment-sdk-setup-local.md).

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer et exécuter un script Python « Hello World » en local
> * Créer un script de contrôle Python pour envoyer « Hello World » à Azure Machine Learning
> * Comprendre les concepts de Azure Machine Learning en matière de script de contrôle
> * Soumettre et exécuter un script « Hello World »
> * Afficher la sortie de votre code dans le cloud

## <a name="prerequisites"></a>Prérequis

- Terminez la [Partie 1 du tutoriel sur la configuration d’une machine locale](tutorial-1st-experiment-sdk-setup-local.md) si vous n’avez pas encore d’espace de travail Azure Machine Learning.
- Avoir des connaissances préliminaires du langage Python et des workflows Machine Learning.
- Avoir un environnement de développement local. Cet environneent doit se composer notamment de Visual Studio Code, Jupyter ou PyCharm.
- Avoir au moins la version 3.5-3.7 de Python.

## <a name="create-and-run-a-python-script-locally"></a>Créer et exécuter un script Python en local

Créez un sous-répertoire appelé `src` sous le répertoire `tutorial` pour stocker le code que vous souhaitez exécuter sur un cluster de calcul Azure Machine Learning. Dans le sous-répertoire `src`, créez le script Python `hello.py`  :

```python
# src/hello.py
print("Hello world!")
```

La structure du répertoire de votre projet doit maintenant ressembler à ce qui suit :

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Valider votre script en local

Vous pouvez exécuter votre code en local, ce qui présente l’avantage du débogage interactif de code, à l’aide de votre IDE favori ou via un terminal :

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Créer un script de contrôle

Un *script de contrôle* vous permet d’exécuter votre script `hello.py` dans le cloud.  Le script de contrôle vous permet de contrôler le mode et l’emplacement d’exécution de votre code Machine Learning.  

Dans le répertoire de votre tutoriel, créez un nouveau fichier Python appelé `03-run-hello.py` et copiez-collez le code ci-dessous dans ce fichier :

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Comprendre le code

Voici une description du fonctionnement du script de contrôle :

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [L’espace de travail](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) se connecte à votre espace de travail Azure Machine Learning, afin que vous puissiez communiquer avec vos ressources Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [L’expérience](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) offre un moyen simple d’organiser plusieurs exécutions sous un nom unique. Plus tard, vous pourrez voir comment les expériences facilitent la comparaison des mesures entre des dizaines d’exécutions.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) encapsule votre code `hello.py` et le transmet à votre espace de travail. Comme son nom l’indique, vous pouvez utiliser cette classe pour _configurer_ la manière dont vous souhaitez que votre _script_ _s’exécute_ dans Azure Machine Learning. Spécifie également la cible de calcul sur laquelle le script s’exécutera.  Dans ce code, la cible est le cluster de calcul que vous avez créé dans le [tutoriel de configuration](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Soumet votre script. Cette soumission est appelée [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true).  Un Run encapsule une seule exécution de votre code. Utilisez une série pour surveiller la progression du script, capturer la sortie, analyser les résultats, visualiser les métriques et bien plus encore.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        L’objet `run` fournit un handle sur l’exécution de votre code. Surveillez la progression de Azure Machine Learning Studio à l’aide de l’URL imprimée à partir du script Python.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Soumettre et exécuter votre code dans le cloud

Exécutez votre script de contrôle qui, à son tour, exécute `hello.py` sur le cluster de calcul que vous avez créé dans le [tutoriel de configuration](tutorial-1st-experiment-sdk-setup-local.md).

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>Surveiller votre code dans le cloud à l’aide de Studio

La sortie contient un lien vers Azure Machine Learning Studio qui ressemble à ceci : `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Suivez le lien et accédez à l’onglet **Sorties + Journaux** . Vous pouvez voir un fichier `70_driver_log.txt` comme suit :

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

À la ligne 8, vous voyez apparaître la sortie du script « Hello World ! » .

Le fichier `70_driver_log.txt` contient la sortie standard d’une exécution. Ce fichier peut être utile lors du débogage des exécutions distantes dans le cloud.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez pris un script « Hello World » simple et vous l’avez exécuté sur Azure. Vous avez appris à vous connecter à votre espace de travail Azure Machine Learning, à créer une expérience et à envoyer votre code `hello.py` dans le cloud.

Dans le tutoriel suivant, vous allez développer ces apprentissages en exécutant un nom plus intéressant que `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Tutoriel : entraîner un modèle](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Si vous souhaitez terminer la série de tutoriels dès maintenant, sans passer à l’étape suivante, n’oubliez pas de [nettoyer vos ressources](tutorial-1st-experiment-bring-data.md#clean-up-resources)
