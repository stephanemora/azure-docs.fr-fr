---
title: Démarrer, surveiller et annuler les exécutions de formation dans Python
titleSuffix: Azure Machine Learning service
description: Découvrez comment démarrer, définir l’état de la balise et organiser vos expériences d’apprentissage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543591"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Démarrer, surveiller et annuler les exécutions de formation dans Python

Le [Azure Machine Learning SDK pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fournit différentes méthodes pour analyser, organiser et gérer vos séries de formation et d’expérimentation.

Cet article répertorie des exemples des tâches suivantes :

* Analyseur de performances.
* Annuler ou faire échouer s’exécute.
* Créer enfant s’exécute.
* Balise et rechercher les exécutions.

## <a name="prerequisites"></a>Conditions préalables

Vous aurez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Un [espace de travail de service Azure Machine Learning](setup-create-workspace.md).

* Le Kit de développement Azure Machine Learning pour Python (version 1.0.21 ou une version ultérieure). Pour installer ou mettre à jour vers la dernière version du SDK, consultez [installer ou mettre à jour le SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Pour vérifier votre version du SDK Azure Machine Learning, utilisez le code suivant :

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>Démarrer une exécution et son processus de journalisation

Configurer votre expérience en important le [espace de travail](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [faire des essais](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [exécuter](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), et [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) classes à partir de la [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) package.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Démarrer une exécution et son processus de journalisation avec le [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) (méthode).

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

## <a name="monitor-the-status-of-a-run"></a>Surveiller l’état d’une exécution

Obtenir l’état d’une exécution avec le [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) (méthode).

```Python
print(notebook_run.get_status())
```

Pour obtenir des détails supplémentaires sur l’exécution, utilisez la [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) (méthode).

```Python
notebook_run.get_details()
```

Lorsque votre série de tests se termine correctement, utilisez le [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) méthode à marquer comme terminé.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Si vous utilisez de Python `with...as` modèle, l’exécution marque automatiquement lui-même comme terminé lorsque l’exécution est hors de portée. Vous n’avez pas besoin de marquer manuellement l’exécution comme étant terminés.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>Annuler ou d’échec des exécutions

 Si vous remarquez une erreur ou si votre série de tests prend trop de temps à la fin, utilisez le [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) méthode pour arrêter l’exécution avant la fin et la marquer comme étant annulée.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Si votre série de tests terminée, mais il contient une erreur (par exemple, le script de formation incorrect a été utilisé), vous pouvez utiliser la [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) méthode pour la marquer comme ayant échoué.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>Créer des séries d’enfant

Créer enfant s’exécute pour regrouper des séries associées, comme pour les différentes itérations de réglage des hyperparamètres.

Cet exemple de code utilise le `hello_with_children.py` script de création d’un lot de cinq enfant s’exécute à partir d’une exécution soumise à l’aide de la [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) méthode :

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Comme ils passent hors de portée, enfant exécutions sont automatiquement marquées comme terminées.

Vous pouvez également démarrer enfant s’exécute un par un, mais étant donné que chaque création entraîne un appel réseau, il est moins efficace que l’envoi d’un lot d’exécutions.

Pour interroger les exécutions de l’enfant d’un parent spécifique, utilisez le [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) (méthode).

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Balise et rechercher les exécutions

Dans le service Azure Machine Learning, vous pouvez utiliser des balises et propriétés pour aider à organiser et interroger vos exécutions pour des informations importantes.

### <a name="add-properties-and-tags"></a>Ajouter des propriétés et balises

Pour ajouter des métadonnées de recherche à vos exécutions, utilisez le [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) (méthode). Par exemple, le code suivant ajoute le `"author"` propriété à l’exécution :

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Propriétés sont immuables, ils créent un suivi permanent à des fins d’audit. Le code suivant génère une erreur, exemple étant donné que nous avons déjà ajouté `"azureml-user"` comme le `"author"` valeur de propriété dans le code précédent :

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Contrairement aux propriétés, les balises sont modifiables. Pour ajouter des informations consultable et significatives pour les consommateurs de votre expérience, utilisez le [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) (méthode).

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Vous pouvez également ajouter des balises de chaîne simple. Lorsque ces balises s’affichent dans le dictionnaire de balise, ils ont une valeur de `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Les balises et les propriétés de la requête

Vous pouvez interroger s’exécute au sein d’une expérience pour retourner une liste d’exécutions qui correspondent aux propriétés spécifiques et balises.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Exemples de notebooks

Les blocs-notes suivants illustrent les concepts dans cet article :

* Pour en savoir plus sur les API de journalisation, consultez le [bloc-notes de journalisation API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Pour plus d’informations sur la gestion s’exécute avec le Kit de développement logiciel Azure Machine Learning, consultez le [gérer les exécutions bloc-notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment enregistrer des métriques pour vos expériences, consultez [journaliser les métriques pendant les exécutions d’apprentissage](how-to-track-experiments.md).