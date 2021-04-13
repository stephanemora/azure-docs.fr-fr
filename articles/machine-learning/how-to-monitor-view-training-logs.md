---
title: Surveiller et consulter les journaux d’exécution et les métriques de Machine Learning
titleSuffix: Azure Machine Learning
description: Surveillez vos expériences ML et consultez les métriques d’exécution avec les widgets Jupyter et Azure Machine Learning Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c258ac62617cb6ac954e0b8c59928225c7f477b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935569"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Surveiller et consulter les journaux d’exécution et les métriques de Machine Learning

Découvrez comment superviser les exécutions Azure Machine Learning et en consulter les journaux. 

Quand vous exécutez une expérience, les journaux et les métriques sont envoyés en streaming pour vous.  Vous pouvez aussi ajouter les vôtres.  Pour savoir comment procéder, consultez [Activer la journalisation dans les exécutions d’entraînement Azure Machine Learning](how-to-track-experiments.md).

Les journaux peuvent vous aider à diagnostiquer les erreurs et les avertissements pour votre exécution. Les métriques de performances, telles que les paramètres et la justesse du modèle, vous aident à suivre et à superviser vos exécutions.

Cet article vous montre comment consulter les journaux à l’aide des méthodes suivantes :

> [!div class="checklist"]
> * Surveiller les exécutions dans le studio
> * Surveiller les exécutions avec le widget Jupyter Notebook
> * Surveiller les exécutions de Machine Learning automatisé
> * Afficher les journaux de sortie une fois l’opération terminée
> * Afficher les journaux de sortie dans le studio

Pour obtenir des informations générales sur la gestion de vos expériences, consultez [Démarrer, surveiller et annuler des exécutions de formation](how-to-manage-runs.md).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Surveiller les exécutions avec le widget Jupyter Notebook

Lorsque vous utilisez la méthode **ScriptRunConfig** pour envoyer des exécutions, vous pouvez vérifier la progression de l’exécution avec un [widget Jupyter](/python/api/azureml-widgets/azureml.widgets). Comme l’envoi de l’exécution, le widget est asynchrone et fournit des mises à jour automatiques toutes les 10 à 15 secondes jusqu’à ce que la tâche soit terminée.

Affichez le widget Jupyter en attendant la fin de l’exécution.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Capture d’écran du widget de notebook Jupyter](./media/how-to-track-experiments/run-details-widget.png)

Vous pouvez également obtenir un lien vers le même affichage dans votre espace de travail.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Surveiller les exécutions de Machine Learning automatisé

Pour les exécutions de Machine Learning automatisé, pour accéder aux graphiques d’une exécution précédente, remplacez `<<experiment_name>>` par le nom de l’expérience appropriée :

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widget de notebook Jupyter pour le Machine Learning automatisé](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Afficher la sortie une fois l’opération terminée

Quand vous utilisez **ScriptRunConfig**, vous pouvez employer ```run.wait_for_completion(show_output = True)``` pour indiquer quand l’entraînement du modèle est terminé. L’indicateur ```show_output``` vous donne une sortie détaillée. Pour plus d’informations, consultez la section ScriptRunConfig de [Procédure d’activation de la journalisation](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>

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

## <a name="view-run-records-in-the-studio"></a>Consulter les enregistrements d’exécution dans le studio

Vous pouvez parcourir les enregistrements d’exécution terminés, notamment les mesures journalisées, dans le [studio Azure Machine Learning](https://ml.azure.com).

Accédez à l’onglet **Expériences**. Pour afficher toutes les exécutions de votre espace de travail dans Expériences, sélectionnez l’onglet **Toutes les exécutions**. Vous pouvez explorer les exécutions au niveau du détail pour des Expériences spécifiques en appliquant le filtre Expérience dans la barre de menus supérieure.

Pour l’affichage individuel de l’Expérience, sélectionnez l’onglet **Toutes les expériences**. Dans le tableau de bord d’exécution d’expérience, vous pouvez voir les journaux et les métriques suivis pour chaque exécution. 

Vous pouvez également modifier la table de la liste d’exécution pour sélectionner plusieurs exécutions et afficher la valeur la plus récente, la valeur minimale ou la valeur maximale du journal pour vos exécutions. Personnalisez vos graphiques pour comparer les valeurs des mesures journalisées et les agrégats sur plusieurs exécutions. 

![Exécution des détails dans Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-and-download-log-files-for-a-run"></a>Afficher et télécharger des fichiers journaux pour une exécution 

Les fichiers journaux sont une ressource essentielle pour déboguer les charges de travail Azure ML. Vous pouvez descendre dans la hiérarchie jusqu’à une exécution spécifique pour afficher ses journaux et sorties :  

1. Accédez à l’onglet **Expériences**.
1. Sélectionnez le runID associé à une exécution spécifique.
1. Sélectionnez **Sorties + journaux** en haut de la page.
2. Sélectionnez **Télécharger tout** pour télécharger tous vos journaux dans un dossier zip.

:::image type="content" source="media/how-to-monitor-view-training-logs/download-logs.png" alt-text="Capture d’écran de la section Sorties et journaux d’une exécution.":::

Les tableaux ci-dessous montrent le contenu des fichiers journaux dans les dossiers qui apparaissent dans cette section.

> [!NOTE]
> Chaque fichier pour chaque exécution n’est pas nécessairement présent. Par exemple, le fichier 20_image_build_log*.txt apparaît uniquement quand une image est créée (par exemple, quand vous changez l’environnement).

#### <a name="azureml-logs-folder"></a>`azureml-logs`

|Fichier  |Description  |
|---------|---------|
|20_image_build_log.txt     | Journal de génération d’image Docker pour l’environnement d’entraînement (facultatif, un par exécution). Applicable uniquement lors de la mise à jour de votre environnement. Sinon, AML réutilise l’image mise en cache. En cas de réussite, contient les détails du registre d’images pour l’image correspondante.         |
|55_azureml-execution-<node_id>.txt     | Journal stdout/stderr de l’outil hôte, un par nœud. Extrait l’image vers la cible de calcul. Notez que ce journal s’affiche uniquement quand vous avez sécurisé les ressources de calcul.         |
|65_job_prep-<node_id>.txt     |   Journal stdout/stderr du script de préparation de travail, un par nœud. Téléchargez votre code vers la cible de calcul et les magasins de données (le cas échéant).       |
|70_driver_log(_x).txt      |  Journal stdout/stderr du script de contrôle AML et du script d’entraînement de client, un par processus. **Il s’agit de la sortie standard de votre script. C’est là que résident les journaux de votre code (par exemple, les instructions print).** Dans la majorité des cas, c’est là que vous supervisez les journaux.       |
|70_mpi_log.txt     |   Journal du framework MPI (facultatif, un par exécution). Uniquement pour une exécution MPI.   |
|75_job_post-<node_id>.txt     |  Journal stdout/stderr du script de fin de travail, un par nœud. Envoyer les journaux, libérer les ressources de calcul dans Azure.        |
|process_info.json      |   Indique le processus en cours d’exécution sur le nœud.  |
|process_status.json      | Indique l’état du processus, c.-à-d., si un processus n’est pas démarré, en cours d’exécution ou terminé.         |

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
|prep_cmd.txt      |   Journal des ContextManagers entrés lors de l’exécution de `job_prep.py` (partiellement envoyé en streaming vers `azureml-logs/65-job_prep`)       |
|release_cmd.txt     |  Journal des ComtextManagers quittés lors de l’exécution de `job_release.py`        |

#### <a name="other-folders"></a>Autres dossiers

Pour les travaux qui effectuent un entraînement sur des clusters multicalculs, les journaux sont présents pour chaque adresse IP de nœud. La structure de chaque nœud est identique à celle des travaux mononœuds. Il existe un dossier de journaux supplémentaire pour les journaux stderr, stdout et d’exécution générale.

Azure Machine Learning consigne des informations de diverses sources pendant la formation, telles que AutoML ou le conteneur Docker qui exécute le travail de formation. La plupart de ces journaux ne sont pas documentés. Si vous rencontrez des problèmes et que vous contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.

## <a name="monitor-a-compute-cluster"></a>Superviser un cluster de calcul

Pour surveiller les exécutions d’une cible de calcul spécifique dans votre navigateur, procédez comme suit :

1. Dans le [studio Azure Machine Learning](https://ml.azure.com/), sélectionnez votre espace de travail, puis sélectionnez __Calcul__ sur le côté gauche de la page.

1. Sélectionnez __Clusters d’entraînement__ pour afficher la liste des cibles de calcul utilisées pour l’entraînement. Ensuite, sélectionnez le cluster.

    ![Sélectionner le cluster d’entraînement](./media/how-to-track-experiments/select-training-compute.png)

1. Sélectionnez __Exécutions__. La liste des exécutions qui utilisent ce cluster s’affiche. Pour voir les détails d’une exécution, utilisez le lien qui se trouve dans la colonne __Exécution__. Pour voir les détails d’une expérience, utilisez le lien qui se trouve dans la colonne __Expérience__.

    ![Sélectionner des exécutions pour le cluster d’entraînement](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Étant donné que les cibles de calcul de formation sont des ressources partagées, elles peuvent avoir plusieurs exécutions mises en file d’attente ou actives à un moment donné.
    > 
    > Une exécution peut contenir des exécutions enfants. De fait, une tâche d’entraînement peut créer plusieurs entrées.

Une fois l’exécution terminée, elle n’est plus affichée dans cette page. Pour voir des informations sur les exécutions terminées, consultez la section __Expériences__ dans le studio, puis sélectionnez l’expérience et l’exécution. Pour plus d’informations, consultez la section [Consulter les métriques pour les exécutions terminées](#view-the-experiment-in-the-web-portal).


## <a name="next-steps"></a>Étapes suivantes

Essayez de suivre les étapes suivantes pour savoir comment utiliser Azure Machine Learning :

* Découvrez comment [suivre les expériences et activer les journaux dans le concepteur Azure Machine Learning](how-to-track-designer-experiments.md).

* Examinez un exemple d’inscription et de déploiement du meilleur modèle dans le tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).
