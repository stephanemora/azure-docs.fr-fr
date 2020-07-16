---
title: Journaliser les expériences et métriques Machine Learning
titleSuffix: Azure Machine Learning
description: Surveillez vos expériences Azure Machine Learning et les métriques d'exécution pour améliorer le processus de création de modèle. Ajoutez la journalisation à votre script d'entraînement et affichez les résultats enregistrés d’une exécution.  Utilisez run.log, Run.start_logging ou ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: how-to
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 426c79c19b599127e2235f61e8c917062ede3b79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675200"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Surveiller les exécutions et les métriques des expériences Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Améliorez le processus de création de modèle avec le suivi de vos expériences et la supervision des métriques d'exécution. Dans cet article, découvrez comment ajouter du code de journalisation à votre script d’entraînement, soumettre une exécution d’expérience, superviser cette exécution et inspecter les résultats dans Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning peut aussi consigner des informations provenant d’autres sources pendant l’entraînement, notamment les exécutions du Machine Learning automatisé ou le conteneur Docker qui exécute le travail d’entraînement. Ces journaux ne sont pas documentés. Si vous rencontrez des problèmes et que vous contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux scientifiques des données et aux développeurs qui veulent superviser le processus d’entraînement du modèle. Si vous êtes un administrateur intéressé par la supervision de l’utilisation des ressources et des événements d’Azure Machine Learning, comme les quotas, les exécutions d’entraînement réalisées ou les déploiements de modèles terminés, consultez [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Métriques pouvant être suivies

Les métriques suivantes peuvent être ajoutées à une exécution pendant l’entraînement d’une expérimentation. Pour afficher une liste plus détaillée des éléments qui peuvent être suivis lors d’une exécution, consultez la [documentation de référence sur l’exécution de la classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Fonction Python | Notes|
|----|:----|:----|
|Valeurs scalaires |Fonction :<br>`run.log(name, value, description='')`<br><br>Exemple :<br>run.log("accuracy", 0.95) |Journalisez une valeur numérique ou de chaîne dans l’exécution avec le nom donné. La journalisation d’une métrique dans une exécution entraîne le stockage de cette métrique dans l’enregistrement d’exécution dans l’expérimentation.  Vous pouvez consigner la même métrique plusieurs fois pendant une exécution, le résultat étant considéré comme un vecteur de cette métrique.|
|Listes|Fonction :<br>`run.log_list(name, value, description='')`<br><br>Exemple :<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Journalisez une liste de valeurs dans l’exécution avec le nom donné.|
|Ligne|Fonction :<br>`run.log_row(name, description=None, **kwargs)`<br>Exemple :<br>run.log_row("Y over X", x=1, y=0.4) | L’utilisation de *log_row* crée une métrique avec plusieurs colonnes, comme décrit dans kwargs. Chaque paramètre nommé génère une colonne avec la valeur spécifiée.  Vous pouvez appeler *log_row* une seule fois pour consigner un tuple arbitraire ou plusieurs fois dans une boucle pour générer une table complète.|
|Table de charge de travail|Fonction :<br>`run.log_table(name, value, description='')`<br><br>Exemple :<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Journalisez un objet dictionnaire dans l’exécution avec le nom donné. |
|Images|Fonction :<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemple :<br>`run.log_image("ROC", plot=plt)` | Journalisez une image dans l’enregistrement d’exécution. Utilisez log_image pour journaliser un fichier image .PNG ou un tracé matplotlib dans l’exécution.  Ces images seront visibles et comparables dans l’enregistrement d’exécution.|
|Étiqueter une exécution|Fonction :<br>`run.tag(key, value=None)`<br><br>Exemple :<br>run.tag("selected", "yes") | Étiquetez l’exécution avec une clé de chaîne et une valeur de chaîne facultative.|
|Charger un fichier ou un répertoire|Fonction :<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemple :<br>run.upload_file("best_model.pkl", "./model.pkl") | Chargez un fichier sur l’enregistrement d’exécution. Les exécutions capturent automatiquement le fichier dans le répertoire de sortie spécifié, par défaut « ./outputs » pour la plupart des types d’exécutions.  Utilisez upload_file uniquement quand des fichiers supplémentaires doivent être chargés ou qu’aucun répertoire de sortie n’est spécifié. Nous vous suggérons d’ajouter `outputs` au nom afin qu’il soit chargé sur le répertoire outputs. Vous pouvez répertorier tous les fichiers qui sont associés à cet enregistrement d’exécution en appelant `run.get_file_names()`|

> [!NOTE]
> Les métriques pour les valeurs scalaires, listes, lignes et tables peuvent être de type float, integer ou string.

## <a name="choose-a-logging-option"></a>Choisir une option de journalisation

Si vous souhaitez suivre ou superviser votre expérimentation, vous devez ajouter du code pour démarrer la journalisation quand vous envoyez l’exécution. Voici comment déclencher l’envoi d’exécution :
* __Run.start_logging__ : ajoutez des fonctions de journalisation à votre script d’entraînement et démarrez une session de journalisation interactive dans l’expérimentation spécifiée. **start_logging** crée une exécution interactive pour une utilisation dans des scénarios tels que des notebooks. Toutes les métriques qui sont consignées pendant la session sont ajoutées à l’enregistrement d’exécution dans l’expérimentation.
* __ScriptRunConfig__ : ajoutez des fonctions de journalisation à votre script d’entraînement et chargez l’intégralité du dossier de script avec l’exécution.  **ScriptRunConfig** est une classe pour la définition des configurations pour les exécutions de script. Avec cette option, vous pouvez ajouter le code de supervision pour être informé de la fin de l’opération ou pour obtenir un widget visuel à superviser.
* __Journalisation du concepteur__ : ajoutez des fonctions de journalisation à un pipeline de concepteur par glisser-déplacer à l’aide du module __Exécuter un script Python__. Ajoutez du code Python pour journaliser les expériences du concepteur. 

## <a name="set-up-the-workspace"></a>Configurer l’espace de travail
Avant d’ajouter la journalisation et d’envoyer une expérience, vous devez configurer l’espace de travail.

1. Chargez l’espace de travail. Pour en savoir plus sur la définition de la configuration de l’espace de travail, consultez le [fichier de configuration de l’espace de travail](how-to-configure-environment.md#workspace).

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Option 1 : Utiliser start_logging

**start_logging** crée une exécution interactive pour une utilisation dans des scénarios tels que des notebooks. Toutes les métriques qui sont consignées pendant la session sont ajoutées à l’enregistrement d’exécution dans l’expérimentation.

L’exemple suivant entraîne un simple modèle Ridge sklearn localement dans un notebook Jupyter local. Pour en savoir plus sur l’envoi d’expérimentations dans différents environnements, consultez [Configurer des cibles de calcul pour l’entraînement de modèle avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Chargement des données

Cet exemple utilise le jeu de données « diabète », un petit jeu de données bien connu fourni avec scikit-learn. Cette cellule charge le jeu de données et le divise en jeux d’entraînement et de test aléatoires.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Ajouter un suivi
Ajoutez le suivi d’expérimentation à l’aide du kit de développement logiciel (SDK) Azure Machine Learning et chargez un modèle persistant sur l’enregistrement d’exécution de l’expérimentation. Le code suivant ajoute des balises, des journaux d’activité, puis charge un fichier de modèle sur l’exécution de l’expérimentation.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

Le script se termine par ```run.complete()```, ce qui marque l’exécution comme terminée.  Cette fonction est généralement utilisée dans les scénarios de notebooks interactifs.

## <a name="option-2-use-scriptrunconfig"></a>Option n°2 : Utiliser ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) est une classe destinée à la définition des configurations pour les exécutions de script. Avec cette option, vous pouvez ajouter le code de supervision pour être informé de la fin de l’opération ou pour obtenir un widget visuel à superviser.

Cet exemple s’appuie sur le modèle Ridge sklearn de base ci-dessus. Il effectue un simple balayage des paramètres sur les valeurs alpha du modèle pour capturer les métriques et les modèles entraînés dans les exécutions sous l’expérimentation. L’exemple est exécuté localement dans un environnement géré par l’utilisateur. 

1. Créez un script d’entraînement `train.py`.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. Le script `train.py` fait référence à `mylib.py`, qui permet d’obtenir la liste des valeurs alpha à utiliser dans le modèle Ridge.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Configurez un environnement local géré par l’utilisateur.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Envoyez le script ```train.py``` à exécuter dans l’environnement géré par l’utilisateur. L’intégralité de ce dossier de script est envoyée pour l’entraînement, dont le fichier ```mylib.py```.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

## <a name="option-3-log-designer-experiments"></a>Option 3 : Journaliser les expériences du concepteur

Utilisez le module __Exécuter un script Python__ pour ajouter une logique de journalisation à vos expériences de concepteur. Si ce workflow vous permet de journaliser n’importe quelle valeur, il est particulièrement utile pour journaliser les métriques du module __Évaluer le modèle__ pour suivre les performances du modèle à travers les différentes exécutions.

1. Connectez un module __Exécuter un script Python__ à la sortie de votre module __Évaluer le modèle__.

    ![Connecter le module Exécuter un script Python au module Évaluer le modèle](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Collez le code suivant dans l’éditeur de code __Exécuter un script Python__ pour journaliser l’erreur absolue moyenne de votre modèle entraîné :

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1 = None, dataframe2 = None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')

        from azureml.core import Run

        run = Run.get_context()

        # Log the mean absolute error to the current run to see the metric in the module detail pane.
        run.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])

        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        run.parent.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])
    
        return dataframe1,
    ```

## <a name="manage-a-run"></a>Gérer une exécution

L’article [Start, monitor and cancel training runs (Démarrer, surveiller et annuler des exécutions d’entraînement)](how-to-manage-runs.md) met en avant les flux de travail Azure Machine Learning spécifiques pour la gestion de vos expériences.

## <a name="view-run-details"></a>Afficher les détails de l’exécution

### <a name="view-activequeued-runs-from-the-browser"></a>Afficher les exécutions actives ou en file d’attente dans le navigateur

Les cibles de calcul utilisées pour l’entraînement des modèles sont des ressources partagées. Par conséquent, il peut arriver que plusieurs exécutions soient actives ou se trouvent en file d’attente au même moment. Pour voir les exécutions d’une cible de calcul dans votre navigateur, effectuez les étapes suivantes :

1. Dans [Azure Machine Learning Studio](https://ml.azure.com/), sélectionnez votre espace de travail, puis sélectionnez __Calcul__ sur le côté gauche de la page.

1. Sélectionnez __Clusters d’entraînement__ pour afficher la liste des cibles de calcul utilisées pour l’entraînement. Ensuite, sélectionnez le cluster.

    ![Sélectionner le cluster d’entraînement](./media/how-to-track-experiments/select-training-compute.png)

1. Sélectionnez __Exécutions__. La liste des exécutions qui utilisent ce cluster s’affiche. Pour voir les détails d’une exécution, utilisez le lien qui se trouve dans la colonne __Exécution__. Pour voir les détails d’une expérience, utilisez le lien qui se trouve dans la colonne __Expérience__.

    ![Sélectionner des exécutions pour le cluster d’entraînement](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Une exécution peut contenir des exécutions enfants. De fait, une tâche d’entraînement peut créer plusieurs entrées.

Une fois l’exécution terminée, elle n’est plus affichée dans cette page. Pour voir des informations sur les exécutions terminées, consultez la section __Expériences__ dans le studio, puis sélectionnez l’expérience et l’exécution. Pour plus d’informations, consultez la section [Interroger les métriques d’exécution](#queryrunmetrics).

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Superviser l’exécution avec le widget de notebook Jupyter
Quand vous utilisez la méthode **ScriptRunConfig** pour envoyer des exécutions, vous pouvez vérifier la progression de l’exécution avec un [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Comme l’envoi de l’exécution, le widget est asynchrone et fournit des mises à jour automatiques toutes les 10 à 15 secondes jusqu’à ce que la tâche soit terminée.

1. Affichez le widget Jupyter en attendant la fin de l’exécution.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Capture d’écran du widget de notebook Jupyter](./media/how-to-track-experiments/run-details-widget.png)

   Vous pouvez également obtenir un lien vers le même affichage dans votre espace de travail.

   ```python
   print(run.get_portal_url())
   ```

2. **[Pour l’exécution de Machine Learning automatisé]**  Pour accéder aux graphiques à partir d’une exécution précédente. Remplacez `<<experiment_name>>` par le nom d’une expérience appropriée :

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de notebook Jupyter pour le Machine Learning automatisé](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Pour afficher d’autres détails sur un pipeline, cliquez sur le pipeline que vous souhaitez explorer dans la table. Les graphiques apparaissent dans une fenêtre contextuelle d’Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Obtenir des résultats du journal lors de l’achèvement

L’entraînement et la supervision du modèle se produisent en arrière-plan afin que vous puissiez exécuter d’autres tâches pendant que vous attendez. Vous pouvez également attendre que l’entraînement du modèle soit terminé avant d’exécuter davantage de code. Quand vous utilisez **ScriptRunConfig**, vous pouvez employer ```run.wait_for_completion(show_output = True)``` pour indiquer quand l’entraînement du modèle est terminé. L’indicateur ```show_output``` vous donne une sortie détaillée. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Interroger les métriques d’exécution

Vous pouvez afficher les métriques d’un modèle entraîné à l’aide de ```run.get_metrics()```. Vous pouvez désormais obtenir toutes les métriques qui ont été consignées dans l’exemple ci-dessus pour déterminer le meilleur modèle.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Affichez cette expérience dans votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com).

Une fois l’exécution d’une expérimentation terminée, vous pouvez accéder à l’enregistrement d’exécution de l’expérimentation. Vous pouvez accéder à l’historique à partir d’[Azure Machine Learning Studio](https://ml.azure.com).

Accédez à l’onglet Expériences et sélectionnez votre expérience. Vous êtes dirigé vers le tableau de bord d’exécution de l’expérience, sur lequel vous pouvez voir les métriques et graphiques suivis qui sont journalisés pour chaque exécution. 

Vous pouvez modifier la table de la liste d’exécution pour afficher la valeur la plus récente, la valeur minimale ou la valeur maximale du journal pour vos exécutions. Vous pouvez sélectionner ou désélectionner plusieurs exécutions dans la liste d’exécution et les exécutions sélectionnées rempliront les graphiques avec vos données. Vous pouvez également ajouter de nouveaux graphiques ou modifier des graphiques pour comparer les métriques journalisées (minimum, maximum, dernière ou toutes) sur plusieurs exécutions. Pour explorer vos données plus efficacement, vous pouvez également agrandir vos graphiques.

:::image type="content" source="media/how-to-track-experiments/experimentation-tab.gif" alt-text="Exécution des détails dans Azure Machine Learning Studio":::

Vous pouvez descendre dans la hiérarchie jusqu’à une exécution spécifique pour en afficher les sorties ou les journaux d’activité ou télécharger la capture instantanée de l’expérience que vous avez envoyée afin de pouvoir partager le dossier de l’expérience avec d’autres utilisateurs.

### <a name="viewing-charts-in-run-details"></a>Affichage de graphiques dans des détails d’exécution

Il existe différentes manières d’utiliser les API de journalisation pour enregistrer différents types de métriques au cours d’une exécution et de les afficher sous forme de graphiques dans Azure Machine Learning Studio.

|Valeur connectée|Exemple de code| Afficher dans le portail|
|----|----|----|
|Journaliser un tableau de valeurs numériques| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|graphique en courbes à variable unique|
|Journaliser une valeur numérique avec le même nom de métrique utilisé à plusieurs reprises (comme à l’intérieur d’une boucle for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Graphique en courbes à variable unique|
|Journaliser une ligne avec 2 colonnes numériques à plusieurs reprises|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Graphique en courbes à deux variables|
|Journaliser un table avec 2 colonnes numériques|`run.log_table(name='Sine Wave', value=sines)`|Graphique en courbes à deux variables|


## <a name="example-notebooks"></a>Exemples de notebooks
Les notebooks suivants illustrent les concepts de cet article :
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Essayez de suivre les étapes suivantes pour savoir comment utiliser le kit SDK Azure Machine Learning pour Python :

* Examinez un exemple d’inscription et de déploiement du meilleur modèle dans le tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

* Découvrez comment [entraîner des modèles PyTorch avec Azure Machine Learning](how-to-train-pytorch.md).
