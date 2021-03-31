---
title: Journaliser les expériences et métriques Machine Learning
titleSuffix: Azure Machine Learning
description: Activez la journalisation sur vos exécutions de formation ML pour surveiller les métriques d’exécution en temps réel et pour aider à diagnostiquer les erreurs et les avertissements.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9576730d9c4f8d4d237dce9ce8f207ea14b04f45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471598"
---
# <a name="enable-logging-in-ml-training-runs"></a>Activer la journalisation dans les exécutions de formation ML


Le kit de développement logiciel (SDK) Python d’Azure Machine Learning vous permet de journaliser des informations en temps réel à l’aide du package de journalisation Python par défaut et des fonctionnalités spécifiques au kit de développement logiciel (SDK). Vous pouvez journaliser en local et envoyer des journaux à votre espace de travail dans le portail.

Les journaux peuvent vous aider à diagnostiquer les erreurs et les avertissements, ou à effectuer le suivi des métriques de performances, telles que les paramètres et les performances du modèle. Dans cet article, vous allez apprendre à activer la journalisation dans les scénarios suivants :

> [!div class="checklist"]
> * Sessions de formation interactives
> * Envoi de travaux de formation à l’aide de ScriptRunConfig
> * Paramètres `logging` natifs Python
> * Journalisation à partir de sources supplémentaires


> [!TIP]
> Cet article explique comment surveiller le processus de formation du modèle. Si vous êtes intéressé par la supervision de l’utilisation de ressources et d’événements d’Azure Machine Learning, comme des quotas, des cycles de formation accomplis ou des déploiements de modèles effectués, consultez [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Types de données

Vous pouvez journaliser plusieurs types de données, notamment des valeurs scalaires, des listes, des tableaux, des images, des répertoires, etc. Pour plus d’informations et pour obtenir des exemples de code Python pour différents types de données, consultez la [page Exécuter la référence de classe](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Journalisation des métriques d’exécution 

Utilisez les méthodes suivantes dans les API de journalisation pour influencer les visualisations des métriques. Notez les [limites de service](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) pour ces métriques journalisées. 

|Valeur connectée|Exemple de code| Format dans le portail|
|----|----|----|
|Journaliser un tableau de valeurs numériques| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|graphique en courbes à variable unique|
|Journaliser une valeur numérique avec le même nom de métrique utilisé à plusieurs reprises (comme à l’intérieur d’une boucle for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Graphique en courbes à variable unique|
|Journaliser une ligne avec 2 colonnes numériques à plusieurs reprises|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Graphique en courbes à deux variables|
|Journaliser un table avec 2 colonnes numériques|`run.log_table(name='Sine Wave', value=sines)`|Graphique en courbes à deux variables|
|Journaliser une image|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Utilisez cette méthode pour consigner un fichier image ou un tracé matplotlib dans l’exécution. Ces images seront visibles et comparables dans l’enregistrement d’exécution.|

### <a name="logging-with-mlflow"></a>Journalisation avec MLflow
Utilisez MLFlowLogger pour enregistrer les métriques.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Session de journalisation interactive

Les sessions de journalisation interactives sont généralement utilisées dans les environnements notebook. La méthode [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) démarre une session de journalisation interactive. Toutes les métriques qui sont consignées pendant la session sont ajoutées à l’enregistrement d’exécution dans l’expérience. La méthode [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) met fin aux sessions et marque l’exécution comme terminée.

## <a name="scriptrun-logs"></a>Journaux ScriptRun

Dans cette section, vous découvrirez comment ajouter un code de journalisation au sein des exécutions créées lorsque la configuration est effectuée avec ScriptRunConfig. Vous pouvez utiliser la classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) pour encapsuler des scripts et des environnements pour des exécutions reproductibles. Vous pouvez également utiliser cette option pour afficher un widget Jupyter Notebook visuel pour la surveillance.

Cet exemple effectue un balayage de paramètre sur des valeurs alpha et capture les résultats à l’aide de la méthode [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Créez un script de formation incluant la logique de journalisation, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Envoyez le script ```train.py``` à exécuter dans un environnement géré par l’utilisateur. L’intégralité du dossier de script est soumise pour la formation.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    Le paramètre `show_output` active la journalisation détaillée, qui vous permet d’afficher les détails du processus de formation, ainsi que des informations sur les éventuelles cibles de calcul ou ressources à distance. Utilisez le code suivant pour activer la journalisation détaillée lorsque vous soumettez l’expérience.

```python
run = exp.submit(src, show_output=True)
```

Vous pouvez également utiliser le même paramètre dans la fonction `wait_for_completion` lors de l’exécution qui en résulte.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Journalisation Python native

Certains journaux du Kit de développement logiciel (SDK) risquent de contenir une erreur qui exige de définir le niveau de journalisation sur DÉBOGAGE. Pour définir le niveau de journalisation, ajoutez le code suivant à votre script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Sources de journalisation supplémentaire

Azure Machine Learning peut aussi consigner des informations provenant d’autres sources pendant la formation, notamment les exécutions du Machine Learning automatisé ou des conteneurs Docker qui exécutent les travaux. Ces journaux ne sont pas documentés, mais si vous rencontrez des problèmes et contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.

Pour plus d’informations sur la journalisation des métriques dans le concepteur Azure Machine Learning, consultez [Comment journaliser des métriques dans le concepteur](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Exemples de notebooks

Les notebooks suivants illustrent les concepts de cet article :
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez ces articles pour en savoir plus sur l’utilisation d’Azure Machine Learning :

* Découvrez la procédure de [journalisation des métriques dans le concepteur Azure Machine Learning](how-to-track-designer-experiments.md).

* Examinez un exemple d’inscription et de déploiement du meilleur modèle dans le tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).
