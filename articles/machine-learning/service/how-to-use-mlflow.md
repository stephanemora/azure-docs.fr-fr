---
title: Utiliser MLflow avec Azure Machine Learning service
titleSuffix: Azure Machine Learning service
description: Découvrez comment journaliser des métriques et artefacts à l’aide de la bibliothèque MLflow dans Azure Machine Learning service
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: d0bc4620d0c55d6e94a3d99c39ab405dab2743e5
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461649"
---
# <a name="use-mlflow-with-azure-machine-learning-service-preview"></a>Utiliser MLflow avec Azure Machine Learning service (préversion)

Cet article explique comment utiliser l’URI de suivi et l’API de journalisation de MLflow, collectivement appelés Suivi MLflow, avec Azure Machine Learning service pour suivre et journaliser vos métriques et artefacts d’expérimentation dans votre [espace de travail Azure Machine Learning service](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace). Si vous utilisez déjà MLflow Tracking pour vos expériences, l’espace de travail fournit un emplacement centralisé, sécurisé et évolutif pour stocker vos métriques et modèles d’apprentissage.

[MLflow](https://www.mlflow.org) est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning. Le [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) est un composant de MLflow qui journalise et suit vos métriques et artefacts de modèle de cycle d’apprentissage, que vos expériences soient exécutées localement, sur une machine virtuelle ou sur un cluster de calcul distant.
![diagramme de mlflow avec azure machine learning](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparer les clients MLflow et Azure Machine Learning

 Le tableau ci-dessous récapitule les différents clients pouvant utiliser Azure Machine Learning service et leurs fonctionnalités respectives.

 MLflow Tracking offre des fonctionnalités de stockage de journalisation de métriques et d’artefact qui ne sont autrement disponibles que via le [Kit de développement logiciel (SDK) Python Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | MLflow Tracking | Azure Machine Learning <br> Kit de développement logiciel (SDK) Python |  Azure Machine Learning <br> Interface de ligne de commande | Portail Azure|
|-|-|-|-|-|
| Gérer l'espace de travail |   | ✓ |  ✓ | ✓  |
| Utiliser des magasins de données  |   | ✓ |  ✓ |    |
| Journaliser les métriques      | ✓ | ✓ |    |    |
| Charger les artefacts | ✓ | ✓ |    |    |
| Afficher les mesures     | ✓ | ✓ | ✓  | ✓ |
| Gérer le calcul   |   | ✓ | ✓  | ✓ |
| Déployer des modèles    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Prérequis

* [Installez MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installez le Kit de développement logiciel (SDK) Python Azure Machine Learning sur votre ordinateur local et créez un espace de travail Azure Machine Learning Azure](setup-create-workspace.md#sdk). Le Kit de développement logiciel (SDK) fournit la connectivité pour permettre à MLflow d’accéder à votre espace de travail.

## <a name="track-local-runs"></a>Suivre les exécutions locales

Installez le package `azureml-contrib-run` pour utiliser MLflow Tracking avec Azure Machine Learning sur vos expériences exécutées localement dans un Jupyter Notebook ou un éditeur de code.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>L’espace de noms azureml.contrib change fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.

Importez les classes `mlflow` et [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) pour accéder à l’URI de suivi de MLflow et configurer votre espace de travail.

Dans le code suivant, la méthode `get_mlflow_tracking_uri()` attribue un adresse URI de suivi unique à l’espace de travail, `ws`, et `set_tracking_uri()` pointe le L’URI de suivi de MLflow sur cette adresse.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L’URI de suivi est valide pendant au maximum une heure. Si vous redémarrez votre script après un certain temps d’inactivité, utilisez l’API get_mlflow_tracking_uri pour obtenir un nouvel URI.

Définissez le nom de l’expérience MLflow avec `set_experiment()` et commencez votre cycle d’apprentissage avec `start_run()`. Utilisez ensuite `log_metric()` à activer l’API de journalisation de MLflow et commencer la journalisation des métriques de votre cycle d’apprentissage.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Suivre les exécutions à distance

Les exécutions à distance vous permettent d’effectuer l’apprentissage de vos modèles sur des calculs plus puissants, tels que des machines virtuelles activées par GPU ou des clusters de Capacité de calcul Machine Learning. Pour en savoir plus sur les différentes options de calcul, voir [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

Configurez votre environnement de calcul et d’apprentissage avec la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Incluez les packages pip `mlflow` et `azure-contrib-run` dans la section [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) de l’environnement. Construisez ensuite [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) avec votre calcul à distance en tant que cible de calcul.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

Dans votre script d’apprentissage, importez `mlflow` pour utiliser les API de journalisation MLflow et commencez à journaliser vos métriques d’exécution.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

Avec cette configuration du cycle de calcul et d’apprentissage, utilisez la méthode `Experiment.submit("train.py")` pour soumettre un cycle. Cela définit automatiquement l’URI de suivi de MLflow et dirige la journalisation de MLflow vers votre espace de travail.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Afficher les métriques et les artefacts dans votre espace de travail

Les métriques et artefacts provenant de la journalisation de MLflow sont conservés dans votre espace de travail sur le [portail Azure](https://portal.azure.com). Pour les afficher à tout moment, accédez à votre espace de travail et recherchez l’expérience par son nom.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas d’utiliser les métriques et artefacts enregistrés dans votre espace de travail, la possibilité de les supprimer individuellement est actuellement indisponible. Au lieu de cela, supprimez le groupe de ressources contenant le compte de stockage et l’espace de travail afin d’éviter des frais supplémentaires :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche.

   ![Supprimer dans le portail Azure](media/how-to-use-mlflow/delete-resources.png)

1. À partir de la liste, sélectionnez le groupe de ressources créé.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez le nom du groupe de ressources. Puis sélectionnez **Supprimer**.

## <a name="example-notebooks"></a>Exemples de notebooks

Le [bloc-notes MLflow avec Azure Machine Learning](https://aka.ms/azureml-mlflow-examples) illustre les concepts de cet article.

## <a name="next-steps"></a>Étapes suivantes

* [Comment déployer un modèle](how-to-deploy-and-where.md).
