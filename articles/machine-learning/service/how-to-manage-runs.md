---
title: Démarrer, surveiller et annuler les exécutions de formation dans Python
titleSuffix: Azure Machine Learning service
description: Découvrez comment démarrer, définir l’état de la balise et organiser votre expérience d’apprentissage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: 82df2258116ce55fa440b67ec0a66b106d0d72c7
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471492"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Démarrer, surveiller et annuler les exécutions de formation dans Python

Le [Azure Machine Learning SDK pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fournit différentes méthodes pour analyser, organiser et gérer vos séries de formation et d’expérimentation.

Cette procédure montre des exemples des tâches suivantes :

* [Moniteur de performances](#monitor)
* [Annuler ou d’échec des exécutions](#cancel)
* [Créer des séries d’enfant](#children)
* [Balise et rechercher les exécutions](#tag)

## <a name="prerequisites"></a>Conditions préalables

Vous aurez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Un espace de travail de service Microsoft Azure Machine Learning. Consultez [créer un espace de travail du service Azure Machine Learning](setup-create-workspace.md).

* Le kit SDK de Azure Machine Learning pour Python installée (version 1.0.21 ou une version ultérieure). Pour installer ou mettre à jour vers la dernière version du Kit de développement, accédez à la [installer/mettre à jour le SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) page.

    Pour vérifier votre version du SDK Azure Machine Learning, utilisez le code suivant.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-a-run-and-set-its-status"></a>Démarrer une exécution et définir son état

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

Obtenir l’état de l’exécution avec [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

Pour obtenir des informations supplémentaires sur l’utilisation d’exécution [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

Lorsque votre série de tests se termine correctement, utilisez le [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) méthode à marquer comme terminé.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Vous pouvez également utiliser de Python `with...as` modèle. Dans ce contexte, l’exécution sera automatiquement se marquer comme terminé lorsque l’exécution est hors de portée. De cette façon, vous n’avez pas besoin Marquer manuellement l’exécution comme terminé.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Annuler ou d’échec des exécutions

 Si vous constatez une erreur ou de votre série de tests semble prendre un certain temps pour terminer, utilisez la [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) méthode pour arrêter l’exécution avant la fin et la marquer comme étant annulée.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Si votre série de tests se termine, mais il contient le type d’erreur, le script de formation incorrect a été utilisé, vous pouvez utiliser la [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) méthode pour la marquer comme ayant échoué.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Créer des séries d’enfant

Créer enfant s’exécute pour regrouper des séries associées, comme pour les itérations de réglage d’hyperparamètres différents.

Cet exemple de code utilise le script hello_with_children.py pour créer un lot de cinq enfant s’exécute à partir d’une exécution soumis à l’aide du [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) (méthode).

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
> Enfant s’exécute complète automatiquement lorsqu’ils passent hors de portée.

Vous pouvez également démarrer enfant s’exécute un par un, mais étant donné que chaque création entraîne un appel réseau, il est moins efficace que l’envoi d’un lot d’exécutions.

Utilisez le [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) s’exécute de méthode pour interroger l’enfant d’un parent spécifique.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Balise et rechercher les exécutions

Dans le service Azure Machine Learning, vous pouvez utiliser des balises et propriétés pour aider à organiser et interroger vos exécutions pour des informations importantes.

### <a name="add-properties-and-tags"></a>Ajouter des propriétés et balises

Utilisez le [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) pour ajouter les métadonnées de recherche à vos exécutions. Par exemple, le code suivant ajoute la propriété « author » à l’exécution.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Propriétés sont immuables, ce qui est utile en tant qu’un suivi permanent à des fins d’audit. L’exemple de code suivant entraîne une erreur, dans la mesure où nous avons déjà ajouté « azureml-utilisateur » en tant que la propriété « author » dans le code précédent.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Toutefois, les balises, sont modifiables. Utilisez [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) pour ajouter des informations de recherche et explicites pour les consommateurs de votre expérience.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Vous pouvez également ajouter une balise de chaîne simple. Il apparaît dans le dictionnaire de balise avec la valeur de `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Les balises et les propriétés de la requête

Vous pouvez interroger s’exécute au sein d’une expérience qui correspondent aux propriétés spécifiques et balises.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Exemples de notebooks

Les notebooks suivants illustrent les concepts de cet article :

* Pour en savoir plus sur les API de journalisation, consultez le [bloc-notes de journalisation API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Pour plus d’informations sur la gestion s’exécute avec le Kit de développement logiciel Azure Machine Learning, consultez le [gérer les exécutions bloc-notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment enregistrer des métriques pour vos expériences, consultez le [journaliser les métriques pendant les exécutions d’apprentissage](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) article.