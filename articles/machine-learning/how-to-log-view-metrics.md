---
title: Journaliser et afficher les métriques et les fichiers journaux
titleSuffix: Azure Machine Learning
description: Activez la journalisation sur vos exécutions de formation ML pour surveiller les métriques d’exécution en temps réel et pour aider à diagnostiquer les erreurs et les avertissements.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: ce8fe90a88795c7c08708d6a77246d36f3079e4c
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889139"
---
# <a name="log--view-metrics-and-log-files"></a>Journaliser et afficher les métriques et les fichiers journaux

Consignez des informations en temps réel en utilisant à la fois le package de journalisation Python par défaut et les fonctionnalités au Kit de développement logiciel (SDK) Python Azure Machine Learning. Vous pouvez journaliser en local et envoyer des journaux à votre espace de travail dans le portail.

Les journaux peuvent vous aider à diagnostiquer les erreurs et les avertissements, ou à effectuer le suivi des métriques de performances, telles que les paramètres et les performances du modèle. Dans cet article, vous allez apprendre à activer la journalisation dans les scénarios suivants :

> [!div class="checklist"]
> * Journalisation des métriques d’exécution
> * Sessions de formation interactives
> * Envoi de travaux de formation à l’aide de ScriptRunConfig
> * Paramètres `logging` natifs Python
> * Journalisation à partir de sources supplémentaires


> [!TIP]
> Cet article explique comment surveiller le processus de formation du modèle. Si vous êtes intéressé par la supervision de l’utilisation de ressources et d’événements d’Azure Machine Learning, comme des quotas, des cycles de formation accomplis ou des déploiements de modèles effectués, consultez [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Types de données

Vous pouvez journaliser plusieurs types de données, notamment des valeurs scalaires, des listes, des tableaux, des images, des répertoires, etc. Pour plus d’informations et pour obtenir des exemples de code Python pour différents types de données, consultez la [page Exécuter la référence de classe](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Journalisation des métriques d’exécution 

Utilisez les méthodes suivantes dans les API de journalisation pour influencer les visualisations des métriques. Notez les [limites de service](./resource-limits-quotas-capacity.md#metrics) pour ces métriques journalisées. 

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

## <a name="view-run-metrics"></a>Afficher les métriques d’exécution

## <a name="via-the-sdk"></a>Via le kit SDK
Vous pouvez afficher les métriques d’un modèle entraîné à l’aide de ```run.get_metrics()```. Reportez-vous à l’exemple ci-dessous. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>Afficher les métriques d’exécution dans l’interface utilisateur du studio AML

Vous pouvez parcourir les enregistrements d’exécution terminés, notamment les mesures journalisées, dans le [studio Azure Machine Learning](https://ml.azure.com).

Accédez à l’onglet **Expériences**. Pour afficher toutes les exécutions de votre espace de travail dans Expériences, sélectionnez l’onglet **Toutes les exécutions**. Vous pouvez explorer les exécutions au niveau du détail pour des Expériences spécifiques en appliquant le filtre Expérience dans la barre de menus supérieure.

Pour l’affichage individuel de l’Expérience, sélectionnez l’onglet **Toutes les expériences**. Dans le tableau de bord d’exécution d’expérience, vous pouvez voir les journaux et les métriques suivis pour chaque exécution. 

Vous pouvez également modifier la table de la liste d’exécution pour sélectionner plusieurs exécutions et afficher la valeur la plus récente, la valeur minimale ou la valeur maximale du journal pour vos exécutions. Personnalisez vos graphiques pour comparer les valeurs des mesures journalisées et les agrégats sur plusieurs exécutions. Vous pouvez tracer plusieurs métriques sur l’axe Y de votre graphique et personnaliser l’axe X pour tracer vos mesures consignées. 


### <a name="view-and-download-log-files-for-a-run"></a>Afficher et télécharger des fichiers journaux pour une exécution 

Les fichiers journaux sont une ressource essentielle pour déboguer les charges de travail Azure ML. Après avoir soumis un travail de formation, descendez dans la hiérarchie jusqu’à une exécution spécifique pour afficher ses journaux et ses sorties :  

1. Accédez à l’onglet **Expériences**.
1. Sélectionnez le runID associé à une exécution spécifique.
1. Sélectionnez **Sorties + journaux** en haut de la page.
2. Sélectionnez **Télécharger tout** pour télécharger tous vos journaux dans un dossier zip.
3. Vous pouvez également télécharger des fichiers journaux individuellement en choisissant le fichier journal et en sélectionnant **Télécharger**.

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Capture d’écran de la section Sorties et journaux d’une exécution.":::

Les tableaux ci-dessous montrent le contenu des fichiers journaux dans les dossiers qui apparaissent dans cette section.

> [!NOTE]
> Chaque fichier pour chaque exécution n’est pas nécessairement présent. Par exemple, le fichier 20_image_build_log*.txt apparaît uniquement quand une image est créée (par exemple, quand vous changez l’environnement).

#### <a name="azureml-logs-folder"></a>`azureml-logs`

|Fichier  |Description  |
|---------|---------|
|20_image_build_log.txt     | Journal de génération d’image Docker pour l’environnement d’entraînement (facultatif, un par exécution). Applicable uniquement lors de la mise à jour de votre environnement. Sinon, AML réutilise l’image mise en cache. En cas de réussite, contient les détails du registre d’images pour l’image correspondante.         |
|55_azureml-execution-<node_id>.txt     | Journal stdout/stderr de l’outil hôte, un par nœud. Extrait l’image vers la cible de calcul. Notez que ce journal s’affiche uniquement quand vous avez sécurisé les ressources de calcul.         |
|65_job_prep-<node_id>.txt     |   Journal stdout/stderr du script de préparation de travail, un par nœud. Téléchargez votre code vers la cible de calcul et les magasins de données (le cas échéant).       |
|70_driver_log(_x).txt      |  Journal stdout/stderr du script de contrôle AML et du script d’entraînement de client, un par processus. **Sortie standard de votre script. Ce fichier est l’endroit où les journaux de votre code (par exemple, les instructions print) apparaissent.** Dans la majorité des cas, c’est là que vous supervisez les journaux.       |
|70_mpi_log.txt     |   Journal du framework MPI (facultatif, un par exécution). Uniquement pour une exécution MPI.   |
|75_job_post-<node_id>.txt     |  Journal stdout/stderr du script de fin de travail, un par nœud. Envoyer les journaux, libérer les ressources de calcul dans Azure.        |
|process_info.json      |   Indique le processus en cours d’exécution sur le nœud.  |
|process_status.json      | Indique l’état du processus, par exemple si un processus n’est pas démarré, en cours d’exécution ou terminé.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml`

|Fichier  |Description  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   Journal système pour la préparation des travaux        |
|job_release_azureml.log     | Journal système pour la fin des travaux        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id`

Quand le sidecar est activé, les scripts de préparation de travail et de fin de travail sont exécutés dans le conteneur sidecar.  Il existe un dossier pour chaque nœud. 

|Fichier  |Description  |
|---------|---------|
|start_cms.txt     |  Journal de processus qui démarre quand le conteneur sidecar démarre       |
|prep_cmd.txt      |   Journal des ContextManagers entrés lors de l’exécution de `job_prep.py` (une partie de ce contenu sera envoyée en streaming vers `azureml-logs/65-job_prep`)       |
|release_cmd.txt     |  Journal des ComtextManagers quittés lors de l’exécution de `job_release.py`        |

#### <a name="other-folders"></a>Autres dossiers

Pour les travaux qui effectuent un entraînement sur des clusters multicalculs, les journaux sont présents pour chaque adresse IP de nœud. La structure de chaque nœud est identique à celle des travaux mononœuds. Il existe un autre dossier de journaux pour les journaux stderr, stdout et d’exécution générale.

Azure Machine Learning consigne les informations provenant de diverses sources pendant la formation, telles qu’AutoML ou le conteneur Docker qui exécute le travail de formation. La plupart de ces journaux ne sont pas documentés. Si vous rencontrez des problèmes et que vous contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.


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

## <a name="other-logging-sources"></a>Autres sources de journalisation

Azure Machine Learning peut aussi consigner des informations provenant d’autres sources pendant la formation, notamment les exécutions du Machine Learning automatisé ou des conteneurs Docker qui exécutent les travaux. Ces journaux ne sont pas documentés, mais si vous rencontrez des problèmes et contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.

Pour plus d’informations sur la journalisation des métriques dans le concepteur Azure Machine Learning, consultez [Comment journaliser des métriques dans le concepteur](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Exemples de notebooks

Les notebooks suivants illustrent les concepts de cet article :
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez ces articles pour en savoir plus sur l’utilisation d’Azure Machine Learning :

* Examinez un exemple d’inscription et de déploiement du meilleur modèle dans le tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).