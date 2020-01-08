---
title: Déboguer et détecter un problème ParallelRunStep
titleSuffix: Azure Machine Learning
description: Déboguez et résolvez les problèmes de pipelines de machine learning dans le SDK Azure Machine Learning pour Python. Découvrez les écueils habituels du développement avec des pipelines ainsi que des conseils pour vous aider à déboguer les scripts avant et pendant l’exécution à distance.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: 20e88da6721a6715a7f31f5c423e305625528383
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535345"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Déboguer et détecter un problème à l’aide de ParallelRun
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez découvrir comment déboguer et détecter un problème de la classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) à partir du [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Tester les scripts localement

Consultez la [section Tester les scripts localement](how-to-debug-pipelines.md#testing-scripts-locally) pour les pipelines de Machine Learning (ML). Votre ParallelRunStep s’exécute comme une étape dans les pipelines ML, de sorte que la même réponse s’applique aux deux.

## <a name="debugging-scripts-from-remote-context"></a>Déboguer les scripts à partir du contexte distant

La transition du débogage d’un script de scoring localement au débogage d’un script de scoring dans un pipeline réel peut être difficile. Pour plus d’informations sur la recherche de vos journaux dans le portail, consultez la [section Pipelines de Machine Learning sur le débogage de scripts à partir d’un contexte distant](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Les informations contenues dans cette section s’appliquent également à une exécution d’inférence par lots.

Par exemple, le fichier journal `70_driver_log.txt` contient également les éléments suivants : 

* Toutes les instructions imprimées pendant l’exécution de votre script.
* La rapport des appels de procédure du script.

En raison de la nature distribuée des tâches d’inférence par lots, il existe des journaux provenant de plusieurs sources différentes. Toutefois, deux fichiers consolidés sont créés et fournissent des informations de haut niveau :

- `~/logs/overview.txt`: Ce fichier fournit des informations de haut niveau sur le nombre de mini-lots (également appelés tâches) créés jusqu’à présent et le nombre de mini-lots traités jusqu’à présent. À cette fin, il affiche le résultat du travail. Si le travail a échoué, le message d’erreur s’affiche et indique où démarrer la résolution des problèmes.

- `~/logs/master.txt`: Ce fichier fournit l’affichage du nœud principal (également connu sous le nom d’orchestrateur) du travail en cours d’exécution. Comprend la création de tâches, la surveillance de la progression et le résultat de l’exécution.

Lorsque vous avez besoin de comprendre en détail la façon dont chaque nœud a exécuté le script de score, examinez les journaux de processus individuels pour chaque nœud. Les journaux de processus se trouvent dans le dossier `worker`, regroupés par nœuds Worker :

- `~/logs/worker/<ip_address>/Process-*.txt`: Ce fichier fournit des informations détaillées sur chaque mini-lot au fur et à mesure qu’il est sélectionné ou terminé par un Worker. Pour chaque mini-lot, ce fichier comprend les éléments suivants :

    - L’adresse IP et le PID du processus Worker. 
    - Le nombre total d’éléments et le nombre d’éléments traités avec succès. 
    - L’heure de début et de fin au format horloge (`start1` et `end1`). 
    - L’heure de début et de fin en temps passé par le processeur (`start2` et `end2`). 

Vous pouvez également trouver des informations sur l’utilisation des ressources des processus pour chaque Worker. Ces informations sont au format CSV et se trouvent dans `~/logs/performance/<ip_address>/`. Par exemple, lors de la vérification de l’utilisation des ressources, examinez les fichiers suivants :

- `process_resource_monitor_<ip>_<pid>.csv`: Utilisation des ressources par processus Worker. 
- `sys_resource_monitor_<ip>.csv`: Par journal de nœud.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Comment se connecter à son script utilisateur depuis un contexte distant ?
Vous pouvez configurer un enregistreur d’événements à l’aide des étapes ci-dessous pour afficher les journaux dans le dossier **logs/users** sur le portail :
1. Enregistrez la première section de code ci-dessous dans le fichier entry_script_helper.py et placez le fichier dans le même dossier que votre script d’entrée. Cette classe obtient le chemin d’accès à l’intérieur de AmlCompute. Pour un test local, vous pouvez modifier `get_working_dir()` afin de retourner un dossier local.
2. Configurez un enregistreur d’événements dans votre méthode `init()`, puis utilisez-le. La deuxième section de code ci-dessous est un exemple. 

**entry_script_helper.py :**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**exemple de script d’entrée utilisant l’enregistreur d’événements :**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Étapes suivantes

* Consultez la référence du Kit de développement logiciel (SDK) pour obtenir de l’aide sur le package [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) et la [documentation](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) pour la classe ParallelRunStep.

* Suivez le [tutoriel avancé](tutorial-pipeline-batch-scoring-classification.md) sur l’utilisation de pipelines pour le scoring par lots.
