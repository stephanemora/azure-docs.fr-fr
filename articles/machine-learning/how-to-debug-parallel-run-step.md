---
title: Déboguer et détecter un problème ParallelRunStep
titleSuffix: Azure Machine Learning
description: Déboguez et résolvez les problèmes ParallelRunStep dans les pipelines machine learning dans le SDK Azure Machine Learning pour Python. Découvrez les écueils habituels du développement avec des pipelines ainsi que des conseils pour vous aider à déboguer les scripts avant et pendant l’exécution à distance.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122926"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Déboguer et détecter un problème ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez découvrir comment déboguer et détecter un problème de la classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) à partir du [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Tester les scripts localement

Consultez la [section Tester les scripts localement](how-to-debug-pipelines.md#testing-scripts-locally) pour les pipelines de Machine Learning (ML). Votre ParallelRunStep s’exécute comme une étape dans les pipelines ML, de sorte que la même réponse s’applique aux deux.

## <a name="debugging-scripts-from-remote-context"></a>Déboguer les scripts à partir du contexte distant

La transition du débogage d’un script de scoring localement au débogage d’un script de scoring dans un pipeline réel peut être difficile. Pour plus d’informations sur la recherche de vos journaux dans le portail, consultez la [section Pipelines de Machine Learning sur le débogage de scripts à partir d’un contexte distant](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Les informations contenues dans cette section s’appliquent également à une exécution d’étape parallèle.

Par exemple, le fichier journal `70_driver_log.txt` contient des informations du contrôleur qui lancent un code d’étape d’exécution parallèle.

En raison de la nature distribuée des tâches d’exécution parallèle, il existe des journaux provenant de plusieurs sources différentes. Toutefois, deux fichiers consolidés sont créés et fournissent des informations de haut niveau :

- `~/logs/overview.txt`: Ce fichier fournit des informations de haut niveau sur le nombre de mini-lots (également appelés tâches) créés jusqu’à présent et le nombre de mini-lots traités jusqu’à présent. À cette fin, il affiche le résultat du travail. Si le travail a échoué, le message d’erreur s’affiche et indique où démarrer la résolution des problèmes.

- `~/logs/sys/master.txt`: Ce fichier fournit l’affichage du nœud principal (également connu sous le nom d’orchestrateur) du travail en cours d’exécution. Comprend la création de tâches, la supervision de la progression et le résultat de l’exécution.

Les journaux générés à partir du script d’entrée avec EntryScript.logger et les instructions print se trouvent dans les fichiers suivants :

- `~/logs/user/<ip_address>/Process-*.txt`: Ce fichier contient les journaux écrits à partir d’entry_script avec EntryScript.logger. Il contient également l’instruction print (stdout) d’entry_script.

Lorsque vous avez besoin de comprendre en détail la façon dont chaque nœud a exécuté le script de score, examinez les journaux de processus individuels pour chaque nœud. Les journaux de processus se trouvent dans le dossier `sys/worker`, regroupés par nœuds Worker :

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Ce fichier fournit des informations détaillées sur chaque mini-lot au fur et à mesure qu’il est sélectionné ou terminé par un Worker. Pour chaque mini-lot, ce fichier comprend les éléments suivants :

    - L’adresse IP et le PID du processus Worker. 
    - Le nombre total d’éléments, le nombre d’éléments traités avec succès et le nombre d’éléments ayant échoué.
    - L’heure de début, la durée, le temps de traitement et la durée de la méthode d’exécution.

Vous pouvez également trouver des informations sur l’utilisation des ressources des processus pour chaque Worker. Ces informations sont au format CSV et se trouvent dans `~/logs/sys/perf/<ip_address>/`. Pour un nœud unique, les fichiers de tâche sont disponibles sous `~logs/sys/perf`. Par exemple, lors de la vérification de l’utilisation des ressources, examinez les fichiers suivants :

- `Process-*.csv`: Utilisation des ressources par processus Worker. 
- `sys.csv`: Par journal de nœud.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Comment se connecter à son script utilisateur depuis un contexte distant ?
Vous pouvez obtenir un enregistreur d’événements à partir d’EntryScript comme indiqué dans l’exemple de code ci-dessous pour que les journaux s’affichent dans le dossier **logs/user** dans le portail.

**exemple de script d’entrée utilisant l’enregistreur d’événements :**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Comment puis-je passer une entrée supplémentaire, par exemple un ou des fichiers contenant une table de recherche, à tous mes collaborateurs ?

Construisez un objet [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contenant l’entrée supplémentaire et inscrivez-le auprès de votre espace de travail. Après cela, vous pouvez y accéder dans votre script d’inférence, par exemple dans votre méthode init(), comme suit :

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Étapes suivantes

* Consultez la référence du Kit de développement logiciel (SDK) pour obtenir de l’aide sur le package [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) et la [documentation](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) pour la classe ParallelRunStep.

* Suivez le [tutoriel avancé](tutorial-pipeline-batch-scoring-classification.md) sur l’utilisation de pipelines avec une étape d’exécution parallèle.
